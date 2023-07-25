# GAS OPTIMIZATIONS

All the gas optimizations are determined based on opcodes and sample tests.

| Count | Issues | Instances | Gas Saved |
| --- | --- | --- | --- |
| [[G-1]](#) | Use of memory instead of storage for struct/array state variables | 3 | 6300 |
| [[G-2]](#) | Emitting storage values instead of the memory one | 11 | 1100 |
| [[G-3]](#) | Use nested if and, avoid multiple check combinations | 3 | 45 |
| [[G-4]](#) | Multiple accesses of a mapping/array should use a local variable cache | 1 | 100 |
| [[G-5]](#) | Redundant External Self Calls  | 3 | 300 |
| [[G-6]](#) | Use calldata instead of memory for function arguments that do not get mutated | 6 | 2100 |
| [[G-7]](#) | Pre-increments and pre-decrements are cheaper than post-increments and post-decrements |1  | 6 |
| [[G-8]](#) | Cache state variables with stack variables | 2 | 400 |

`Total Gas saves 10351`

### [G-01] Use of `memory` instead of `storage` for struct/array state variables

When fetching data from `storage`, using the `memory` keyword to assign it to a variable reads all fields of the struct/array and incurs a Gcoldsload (**2100 gas**) for each field.

This can be avoided by declaring the variable with the `storage` keyword and caching the necessary fields in stack variables.

The `memory` keyword should only be used if the entire struct/array is being returned or passed to a function that requires `memory`, or if it is being read from another `memory` array/struct.

`Gas save 6300`

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L170

```solidity
File: [/contracts/core/Vault.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L170) 

170: IVaultController.CollateralInfo memory _collateralInfo = CONTROLLER.tokenCollateralInfo(_tokenAddresses[_i]);
```

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L452

```solidity
File: [/contracts/governance/GovernorCharlie.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L452)

449: function getReceipt(
450:    uint256 _proposalId,
451:    address _voter
452:  ) external view override returns (Receipt memory _votingReceipt) { //@audit
453:    _votingReceipt = proposalReceipts[_proposalId][_voter];
454:  }
```

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L260C7-L260C104

```solidity
File: /contracts/core/VaultController.sol

260: CollateralInfo memory _collateral = _oldVaultController.tokenCollateralInfo(_tokenAddresses[_i]);
```

## [G-02] **Emitting storage values instead of the memory one**

Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:

 `saves 100 ,per instances = 1100`

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L598

```solidity
File: [/contracts/governance/GovernorCharlie.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L598)

587: emit NewDelay(_oldTimelockDelay, proposalTimelockDelay);

598: emit NewEmergencyDelay(_oldEmergencyTimelockDelay, emergencyTimelockDelay);

609: emit VotingDelaySet(_oldVotingDelay, votingDelay);

620: emit VotingPeriodSet(_oldVotingPeriod, votingPeriod);

631: emit EmergencyVotingPeriodSet(_oldEmergencyVotingPeriod, emergencyVotingPeriod);

642: emit ProposalThresholdSet(_oldProposalThreshold, proposalThreshold);

653: emit NewQuorum(_oldQuorumVotes, quorumVotes);

664: emit NewEmergencyQuorum(_oldEmergencyQuorumVotes, emergencyQuorumVotes);

688: emit WhitelistGuardianSet(_oldGuardian, whitelistGuardian);

699: emit WhitelistGuardianSet(_oldGuardian, whitelistGuardian);

710: emit OptimisticQuorumVotesSet(_oldOptimisticQuorumVotes, optimisticQuorumVotes);
```

## [G-03] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

Saves `15 GAS, Per instance` `= 45`

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L158C2-L158C91

```solidity
File: [/contracts/core/Vault.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L158C2-L158C91)

158: if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;
```

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L88

```solidity
File: [/contracts/core/AMPHClaimer.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L88) 

88: if (_crvAmountToSend != 0 && _claimedAmph != 0) distributedAmph += (_claimedAmph / 1e12); // scale back to 1e6
```

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L1018

```solidity
File: [/contracts/core/VaultController.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L1018)

1018: if (_increase && (_collateral.totalDeposited + _amount) > _collateral.cap) revert VaultController_CapReached();
```

## [G-04] Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time. **Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. As an example, instead of repeatedly calling `someMap[someIndex]`, save its reference like this: `SomeStruct storage someStruct = someMap[someIndex]` and use it.

`gas save 42,per call = 100`

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L142

```solidity
File: [/contracts/core/Vault.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L142)

139: function stakeCrvLPCollateral(address _tokenAddress) external override onlyMinter {
140:    uint256 _poolId = CONTROLLER.tokenPoolId(_tokenAddress);
141:    if (_poolId == 0) revert Vault_TokenCanNotBeStaked();
142:    if (balances[_tokenAddress] == 0) revert Vault_TokenZeroBalance();   //@audit 1st call
143:    if (isTokenStaked[_tokenAddress]) revert Vault_TokenAlreadyStaked(); 
144:    isTokenStaked[_tokenAddress] = true;
145:
146:    IBooster _booster = CONTROLLER.BOOSTER();
147:    _depositAndStakeOnConvex(_tokenAddress, _booster, balances[_tokenAddress], _poolId); //@audit 2nd call
148:
149:    emit Staked(_tokenAddress, balances[_tokenAddress]); //@audit 3rd call
150:  }
```

```diff
File: [/contracts/core/Vault.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L142)

139: function stakeCrvLPCollateral(address _tokenAddress) external override onlyMinter {
140:    uint256 _poolId = CONTROLLER.tokenPoolId(_tokenAddress);
+       Balances calldata balance = balances[_tokenAddress];
141:    if (_poolId == 0) revert Vault_TokenCanNotBeStaked();
-142:    if (balances[_tokenAddress] == 0) revert Vault_TokenZeroBalance();
+142:    if (balance == 0) revert Vault_TokenZeroBalance();
143:    if (isTokenStaked[_tokenAddress]) revert Vault_TokenAlreadyStaked();
144:    isTokenStaked[_tokenAddress] = true;
145:
146:    IBooster _booster = CONTROLLER.BOOSTER();
-147:    _depositAndStakeOnConvex(_tokenAddress, _booster, balances[_tokenAddress], _poolId);
+147:    _depositAndStakeOnConvex(_tokenAddress, _booster, balance, _poolId);
148:
-149:    emit Staked(_tokenAddress, balances[_tokenAddress]);
+149:    emit Staked(_tokenAddress, balance);
150:  }
```

### [G-05] Redundant External Self Calls

The referenced statements perform an external self-call as they specify `this.balanceOf` instead of `balanceOf` directly, instructing the compiler to treat the statement as an external call to the `address(this)` target. We advise the code to utilize `balanceOf` directly, avoiding the significant gas overhead of external calls. 

If the code wishes to be verbose about which implementation it invokes, the `this.balanceOf` statements can be replaced by `sUSD.balanceOf`, instructing the compiler to invoke the `sUSD` implementation of `balanceOf` explicitly.

`Gas save (3*100)= 300`

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L131

```solidity
File: /contracts/core/USDA.sol

131: uint256 _balance = this.balanceOf(_msgSender());

141: uint256 _balance = this.balanceOf(_msgSender());

150: if (_susdAmount > this.balanceOf(_msgSender())) revert USDA_InsufficientFunds();
```

### [G-06] Use calldata instead of memory for function arguments that do not get mutated

When you specify a data location as memory, that value will be copied into memory. When you specify the location as calldata, the value will stay static within calldata. If the value is a large, complex type, using memory may result in extra memory expansion costs.

`gas save 2.1K`

**use calldata on signature and data (Save 520 gas on average)**

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IGovernorCharlie.sol#L207

```solidity
File:  /interfaces/governance/IGovernorCharlie.sol

207: function executeTransaction(
208:    address _target,
209:    uint256 _value,
210:    string memory _signature,
211:    bytes memory _data,
212:    uint256 _eta
213:  ) external payable;
```

```diff
File:  /interfaces/governance/IGovernorCharlie.sol

207: function executeTransaction(
208:    address _target,
209:    uint256 _value,
-210:    string memory _signature,
-211:    bytes memory _data,
+210:        string calldata _signature,
+211:        bytes calldata _data,
212:    uint256 _eta
213:  ) external payable;
```

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164C3-L164C89

```solidity
File: /contracts/core/Vault.sol

164: function claimRewards(address[] memory _tokenAddresses) external override onlyMinter {
```

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVault.sol#L193C2-L193C68

```solidity
File: /interfaces/core/IVault.sol

193: function claimRewards(address[] memory _tokenAddresses) external;
```

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IGovernorCharlie.sol#L181C2-L188C1

```solidity
File: /interfaces/governance/IGovernorCharlie.sol

164: function propose(
    address[] memory _targets,
    uint256[] memory _values,
    string[] memory _signatures,
    bytes[] memory _calldatas,
    string memory _description
  ) external returns (uint256 _proposalId);

181: function proposeEmergency(
    address[] memory _targets,
    uint256[] memory _values,
    string[] memory _signatures,
    bytes[] memory _calldatas,
    string memory _description
  ) external returns (uint256 _proposalId);

230: function getActions(uint256 _proposalId)
    external
    view
    returns (
      address[] memory _targets,
      uint256[] memory _values,
      string[] memory _signatures,
      bytes[] memory _calldatas
    );
```

### [G-07] **Pre-increments and pre-decrements are cheaper than post-increments and post-decrements**

`it saves 6 gas per expression.`

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
File:  /contracts/governance/GovernorCharlie.sol 

186: proposalCount++;
```

### [GAS-08] Cache state variables with stack variables

Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

`gas save 100,per access = 400`

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L41

```solidity
File: /contracts/periphery/CurveMaster.sol

41: function setCurve(address _tokenAddress, address _curveAddress) external override onlyOwner {
42:    if (vaultControllerAddress != address(0)) IVaultController(vaultControllerAddress).calculateInterest(); //@audit vaultControllerAddress
43:    address _oldCurve = curves[_tokenAddress];
44:    curves[_tokenAddress] = _curveAddress;
45:
46:    emit CurveSet(_oldCurve, _tokenAddress, _curveAddress);
47:  }
```

```diff
File: /contracts/periphery/CurveMaster.sol

41: function setCurve(address _tokenAddress, address _curveAddress) external override onlyOwner {
+        address _vaultControllerAddress = vaultControllerAddress ;
-42:    if (vaultControllerAddress != address(0)) IVaultController(vaultControllerAddress).calculateInterest();
+42:    if (_vaultControllerAddress != address(0)) IVaultController(_vaultControllerAddress).calculateInterest();
43:    address _oldCurve = curves[_tokenAddress];
44:    curves[_tokenAddress] = _curveAddress;
45:
46:    emit CurveSet(_oldCurve, _tokenAddress, _curveAddress);
47:  }
```

**In `USDA.sol.recoverDust() : balanceOf(address(this))` should be cache** 

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L215

```solidity
File: /contracts/core/USDA.sol

212: function recoverDust(address _to) external onlyOwner {
    // All sUSD sent directly to the contract is not accounted into the reserveAmount
    // This function allows governance to recover it
215:    uint256 _amount = sUSD.balanceOf(address(this)) - reserveAmount;
216:    sUSD.transfer(_to, _amount);
217:
218:    emit RecoveredDust(owner(), _amount); //
219:  }
```

```diff
File: /contracts/core/USDA.sol

212: function recoverDust(address _to) external onlyOwner {
    // All sUSD sent directly to the contract is not accounted into the reserveAmount
    // This function allows governance to recover it
+       uint _balance = sUSD.balanceOf(address(this))
-215:    uint256 _amount = sUSD.balanceOf(address(this)) - reserveAmount;
+215:    uint256 _amount = _balance - reserveAmount;
216:    sUSD.transfer(_to, _amount);
217:
218:    emit RecoveredDust(owner(), _amount);
219:  }
```