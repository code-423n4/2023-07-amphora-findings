| Number | Optimization Details                                                                                                                                                   | Instances |
| :----: | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------: |
| [G-01] | Use assembly to check for the zero address                                                                                                                             |     4     |
| [G-02] | State variables access within a loop                                                                                                                                   |     2     |
| [G-03] | Use of memory instead of storage for struct/array state variables                                                                                                      |     3     |
| [G-04] | Avoid contract existence checks by using low level calls                                                                                                               |     9     |
| [G-05] | Using ternary operator instead of if else saves gas                                                                                                                    |     5     |
| [G-06] | Cache state variables with stack variables                                                                                                                             |     18     |
| [G-07] | Do not calculate constants variables                                                                                                                                   |     5     |
| [G-08] | The use of a logical AND in place of double if is slightly less gas efficient in instances where there isn't a corresponding else statement for the given if statement |     4     |
| [G-09] | Using pre instead of post increments/decrements                                                                                                                        |     2     |
| [G-10] | Non efficient zero initialization                                                                                                                                      |     3     |

Total 10 issues

## [G-01] Use assembly to check for the zero address

Using assembly for address comparisons in Solidity can save gas because it allows for more direct access to the Ethereum Virtual Machine (EVM), reducing the overhead of higher-level operations. Solidity's high-level abstraction simplifies coding but can introduce additional gas costs. Using assembly for simple operations like address comparisons can be more gas-efficient.

_Total 4 instances - 3 file:_

### Instance#1:

```solidity
File: core/solidity/contracts/core/VaultController.sol
112: if (address(_oldVaultController) != address(0)) _migrateCollateralsFrom(_oldVaultController, _tokenAddresses);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L112C5-L112C115

### Instance#2-3:

```solidity
File: core/solidity/contracts/core/Vault.sol
207: if (address(_amphClaimer) != address(0)) {

269: if (address(_amphClaimer) != address(0)) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L207C6-L207C49

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L269

### Instance#4:

```solidity
File: core/solidity/contracts/periphery/CurveMaster.sol
42: if (vaultControllerAddress != address(0)) IVaultController(vaultControllerAddress).calculateInterest();
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L42

## [G-02] State variables access within a loop

State variable reads and writes are more expensive than local variable reads and writes. Therefore, it is recommended to replace state variable reads and writes within loops with a local variable. Gas savings should be multiplied by the average loop length

_Total 2 instances - 1 file:_

### Instance#1-2:

```solidity
File: core/solidity/contracts/core/VaultController.sol
//@audit enabledTokens
868:for (uint192 _i; _i < enabledTokens.length; ++_i) {

//@audit enabledTokens
896:for (uint192 _i; _i < enabledTokens.length; ++_i) {

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L868

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L896

## [G-03] Use of memory instead of storage for struct/array state variables

When fetching data from storage, using the memory keyword to assign it to a variable reads all fields of the struct/array and incurs a Gcoldsload (2100 gas) for each field.

This can be avoided by declaring the variable with the storage keyword and caching the necessary fields in stack variables.

The memory keyword should only be used if the entire struct/array is being returned or passed to a function that requires memory, or if it is being read from another memory array/struct.

_Total 3 instances - 1 file:_

### Instance#1:

```solidity
File: core/solidity/contracts/core/VaultController.sol
260: CollateralInfo memory _collateral = _oldVaultController.tokenCollateralInfo(_tokenAddresses[_i]);

729:CollateralInfo memory _collateral = tokenAddressCollateralInfo[_assetAddress];

751: CollateralInfo memory _collateral = tokenAddressCollateralInfo[_assetAddress];
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L260C7-L260C104

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L729

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L751

## [G-04] Avoid contract existence checks by using low level calls

Prior to Solidity 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls.

In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence.

_Total 9 instances - 2 file:_

### Instance#1-6:

```solidity
File: core/solidity/contracts/core/VaultController.sol
341:uint8 _tokenDecimals = IERC20Metadata(_tokenAddress).decimals();

347:_collateral.crvRewardsContract = IBaseRewardPool(_crvRewards);

361:collateral.oracle = IOracleRelay(_oracleAddress);

399: _collateral.crvRewardsContract = IBaseRewardPool(_crvRewards);

403:_collateral.oracle = IOracleRelay(_oracleAddress);

849:IVault _vault = IVault(_vaultAddress);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L341C5-L341C69

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L347

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L361

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L399

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L403

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L849

### Instance#7-9:

```solidity
File: core/solidity/contracts/core/WUSDA.sol
215: IERC20(USDA).safeTransferFrom(_from, address(this), _usdaAmount);

228:IERC20(USDA).safeTransfer(_to, _usdaAmount);

236:_totalUsdaSupply = IERC20(USDA).totalSupply();
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L215C4-L215C70

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L228

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L236C4-L236C51

## [G-05] Using ternary operator instead of if else saves gas

_Total 5 instances - 4 file:_

### Instance#1:

```solidity
File: core/solidity/contracts/core/VaultController.sol
549:if (_isUSDA) {
550:      // now send usda to the target, equal to the amount they are owed
551:      usda.vaultControllerMint(_target, _amount);
552:    } else {
553:      // send sUSD to the target from reserve instead of mint
554:      usda.vaultControllerTransfer(_target, _amount);
555:    }
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L549C5-L555C6

### Instance#2:

```solidity
File: core/solidity/contracts/core/VaultController.sol
590:if (_repayAll) {
591:      // store the vault baseLiability in memory
592:      _baseAmount = _safeu192(_vault.baseLiability());
593:      // get the total USDA liability, equal to the interest factor * vault's base liabilty
594:      _amountInUSDA = _safeu192(_truncate(interest.factor * _baseAmount));
595:    } else {
596:      // the base amount is the amount of USDA entered divided by the interest factor
597:      _baseAmount = _safeu192((_amountInUSDA * EXP_SCALE) / interest.factor);
598:    }
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L590C5-L598C6

### Instance#3:

```solidity
File: core/solidity/contracts/governance/GovernorCharlie.sol
370:if (
371:          (amph.getPriorVotes(_proposal.proposer, (block.number - 1)) >= proposalThreshold)
372:            || msg.sender != whitelistGuardian
373:        ) revert GovernorCharlie_WhitelistedProposer();
374:      } else {
375:        if ((amph.getPriorVotes(_proposal.proposer, (block.number - 1)) >= proposalThreshold)) {
376:          revert GovernorCharlie_ProposalAboveThreshold();
377:        }
378:     }
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L370C9-L378C8

### Instance#4:

```solidity
File: core/solidity/contracts/core/AMPHClaimer.sol
228:if (_amphAvailableForThisCliff < _amphForThisTurn) {
229:        /// surpassing the cliff
230:        _amphForThisTurn = _amphAvailableForThisCliff;
231:        // calculate how many CRV are entering this cliff
232:        uint256 _amountTokenToEnter = (_amphAvailableForThisCliff * 1e18) / _rate;
233:        _tempAmountReceived -= _amountTokenToEnter;
234:      } else {
235:        /// within the cliff
236:        _tempAmountReceived = 0;
237:      }
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L228C7-L237C8

### Instance#5:

```solidity
File: core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol
83:if (IChainlinkOracleRelay(address(mainRelay)).isStale()) {
84:      /// If the price is stale the range percentage is smaller
85:      _buffer = (staleWidthNumerator * _anchorPrice) / staleWidthDenominator;
86:    } else {
87:      _buffer = (widthNumerator * _anchorPrice) / widthDenominator;
88:    }
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L83C5-L88C6

## [G-06] Cache state variables with stack variables

Caching of a state variable replaces each Gwarmaccess (100 gas) with a cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

_Total 18 instances - 5 file:_

### Instance#1:

```solidity
File: core/solidity/contracts/core/VaultController.sol
//@audit vaultsMinted
992:if (_stop > vaultsMinted) _stop = vaultsMinted;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L992C5-L992C52

### Instance#2-13:

```solidity
File: core/solidity/contracts/governance/GovernorCharlie.sol
//@audit quorumVotes on line 168
203:quorumVotes: quorumVotes,

//@audit use _proposalTimelockDelay instead of proposalTimelockDelay
587:emit NewDelay(_oldTimelockDelay, proposalTimelockDelay);

//@audit use _emergencyTimelockDelay instead of emergencyTimelockDelay
598:emit NewEmergencyDelay(_oldEmergencyTimelockDelay, emergencyTimelockDelay);

//@audit use _newVotingDelay instead of votingDelay
609:emit VotingDelaySet(_oldVotingDelay, votingDelay);

//@audit use _newVotingPeriod instead of votingPeriod
620: emit VotingPeriodSet(_oldVotingPeriod, votingPeriod);

 //@audit use _newEmergencyVotingPeriod instead of  emergencyVotingPeriod
631: emit EmergencyVotingPeriodSet(_oldEmergencyVotingPeriod, emergencyVotingPeriod);

//@audit use _newProposalThreshold instead of proposalThreshold
642: emit ProposalThresholdSet(_oldProposalThreshold, proposalThreshold);

//@audit use _newQuorumVotes instead of quorumVotes
653: emit NewQuorum(_oldQuorumVotes, quorumVotes);

//@audit use _newEmergencyQuorumVotes instead of emergencyQuorumVotes
664: emit NewEmergencyQuorum(_oldEmergencyQuorumVotes, emergencyQuorumVotes);

//@audit use _account instead of  whitelistGuardian
688: emit WhitelistGuardianSet(_oldGuardian, whitelistGuardian);

//@audit use _newOptimisticVotingDelay instead of  optimisticVotingDelay
699: emit OptimisticVotingDelaySet(_oldOptimisticVotingDelay, optimisticVotingDelay);

//@audit use _newOptimisticQuorumVotes instead of optimisticQuorumVotes
710:emit OptimisticQuorumVotesSet(_oldOptimisticQuorumVotes, optimisticQuorumVotes);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L203

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L587

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L598C5-L598C80

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L609

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L620C5-L620C58

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L631

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L642

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L653

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L664

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L688

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L699

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L710

### Instance#14:

```solidity
File: core/solidity/contracts/core/Vault.sol
//@audit balances[_tokenAddress] on line 148,142
150:emit Staked(_tokenAddress, balances[_tokenAddress]);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L150C5-L150C57

### Instance#15-16:

```solidity
File: core/solidity/contracts/core/USDA.sol
//@audit rserveAmount
132:_susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance;

//@audit rserveAmount
142: _susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L132C5-L132C74

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L142C4-L142C74

### Instance#17-18:

```solidity
File:core/solidity/contracts/periphery/CurveMaster.sol
//@audit curves[_tokenAddress] on line 23
24:ICurveSlave _curve = ICurveSlave(curves[_tokenAddress]);

//@audit vaultControllerAddress
42: if (vaultControllerAddress != address(0)) IVaultController(vaultControllerAddress).calculateInterest();
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L24

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L42C4-L42C108

## [G-07] Do not calculate constant variables

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

_Total 5 instances - 3 file:_

### Instance#1-2:

finding are marked as <=FOUND

```solidity
File: core/solidity/contracts/governance/GovernorCharlie.sol
19: bytes32 public constant DOMAIN_TYPEHASH =
20:    keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');//<FOUND

23: bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');//<FOUND
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L19C2-L20C86

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L23C3-L23C99

### Instance#3:

```solidity
File: core/solidity/contracts/core/USDA.sol
21:bytes32 public constant VAULT_CONTROLLER_ROLE = keccak256('VAULT_CONTROLLER');
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L21C3-L21C81

### Instance#4-5:

finding are marked as <=FOUND

```solidity
File: core/solidity/contracts/utils/UFragments.sol
87:bytes32 public constant EIP712_DOMAIN =
88:    keccak256('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)');//<FOUND
89:  bytes32 public constant PERMIT_TYPEHASH =
90:    keccak256('Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)');//<FOUND
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L87C3-L90C101

## [G-08] The use of a logical AND in place of double if is slightly less gas efficient in instances where there isn't a corresponding else statement for the given if statement

Using a double if statement instead of logical AND (&&) can provide similar short-circuiting behavior whereas double if is slightly more efficient.

_Total 4 instances - 3 file:_

### Instance#1:

```solidity
File: core/solidity/contracts/governance/GovernorCharlie.sol
170:if (amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender)) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L170C5-L170C112

### Instance#2:

```solidity
File: core/solidity/contracts/governance/GovernorCharlie.sol
208:if (_emergency && !isWhitelisted(msg.sender)) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L208C5-L208C52

### Instance#3:

```solidity
File: core/solidity/contracts/core/Vault.sol
158: if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L158C4-L158C91

### Instance#4:

```solidity
File: core/solidity/contracts/core/AMPHClaimer.sol
88:if (_crvAmountToSend != 0 && _claimedAmph != 0) distributedAmph += (_claimedAmph / 1e12);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L88C5-L88C94

## [G-09] Using pre instead of post increments/decrements

Pre increments/decrements (++i/--i) are cheaper than post increments/decrements (i++/i--): it saves 6 gas per expression.

_Total 2 instances - 1 file:_

### Instance#1:

```solidity
File: core/solidity/contracts/governance/GovernorCharlie.sol
186:proposalCount++;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L186C5-L186C21

### Instance#2:

```solidity
File: core/solidity/contracts/governance/GovernorCharlie.sol
259:for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L259C5-L259C64

## [G-10] Non efficient zero initialization

Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas.

_Total 3 instances - 1 file:_

### Instance#1-3: Do not initialise the variable i explicity as zero

```solidity
File: core/solidity/contracts/governance/GovernorCharlie.sol
259:for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

313:for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

382:for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L259C5-L259C64

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L313C5-L313C64

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L382
