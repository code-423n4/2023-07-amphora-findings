
# Gas Optimizations Summary

|  NO  | 	Issue  |  Instances  |
|------|-----------|-------------|
|[G-01]| Cache external calls outside of loop to avoid re-calling function on each iteration   | 6 | 
|[G-02]| Combine events to save 2 Glogtopic (375 gas)  | 6 | 
|[G-03]| Use calldata instead of memory for function arguments that do not get mutated   | 10 | 
|[G-04]| Avoid emitting storage values  | 16 | 
|[G-05]| A modifier used only once and not being inherited should be inlined to save gas  | 1 | 
|[G-06]| If statements that use && can be refactored into nested if statements   | 7 | 
|[G-07]| Use assembly to write address storage values   | 14 | 
|[G-08]| Using > 0 costs more gas than != 0 when used on a uint in a require() statement   | 9 | 
|[G-09]| Do not calculate constants   | 8 | 
|[G-10]| Use hardcode address instead address(this)   | 19 | 
|[G-11]| Don’t compare boolean expressions to boolean literals   | 6 | 
|[G-12]| Assigning keccak operations to constant variables results in extra gas costs   | 5 | 
|[G-13]| With assembly, .call (bool success) transfer can be done gas-optimized   | 1 | 
|[G-14]| Unnecessary look up in if condition   | 2 | 
|[G-15]| State variables can be packed into fewer storage slots   | 2 | 
|[G-16]| Use shift Right/Left instead of division/multiplication if possible   | 10 | 
|[G-17]| Amounts should be checked for 0 before calling a transfer   | 5 | 
|[G-18]| internal functions not called by the contract should be removed to save deployment gas   | 3 | 
|[G-19]| Using a positive conditional flow to save a NOT opcode   | 8 | 
|[G-20]| Use do while loops instead of for loops   | 5 | 
|[G-21]| Failure to check the zero address in the constructor causes the contract to be deployed again   | 3 | 
|[G-22]| Inverting the condition of an if-else-statement wastes gas   | 8 | 
|[G-23]| Use assembly to emit events   | 72 | 
|[G-24]| Create immutable variable to avoid redundant external calls   | 1 | 
|[G-25]| Forgo internal function to save 1 STATICCALL   | 3 | 
|[G-26]| Use assembly for math (add, sub, mul, div)   | 10 | 
|[G-27]| Use assembly to validate msg.sender   | 3 | 
|[G-28]|  Use lock_index to look for empty elements   | | 

## [G-01] Cache external calls outside of loop to avoid re-calling function on each iteration

Performing STATICCALLs that do not depend on variables incremented in loops should always try to be avoided within the loop. In the following instances, we are able to cache the external calls outside of the loop to save a STATICCALL (100 gas) per loop iteration.


### Cache CONTROLLER.tokenCollateralInfo(_tokenAddresses[_i]); and also and second loop cache _rewardsContract.extraRewardsLength(); and  _rewardsContract.extraRewards(_j); and third for loop int this contract cache _rewardsContrac### Cache _oldVaultController.tokenCollateralInfo(_tokenAddresses[_i]); outside of loop to save 1 STATICCALL per loop iteration

```solidity
file:   contracts/core/VaultController.sol

255         for (uint256 _i; _i < _tokenAddresses.length;) {
      _tokenId = _oldVaultController.tokenId(_tokenAddresses[_i]);
      if (_tokenId == 0) revert VaultController_WrongCollateralAddress();
      _tokensRegistered++;

      CollateralInfo memory _collateral = _oldVaultController.tokenCollateralInfo(_tokenAddresses[_i]);
      _collateral.tokenId = _tokensRegistered;
      _collateral.totalDeposited = 0;

      enabledTokens.push(_tokenAddresses[_i]);
      tokenAddressCollateralInfo[_tokenAddresses[_i]] = _collateral;

      unchecked {
        ++_i;
      }
    }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L255-L270
t.extraRewards(_i); and _virtualReward.rewardToken(); outside of loop to save 1 STATICCALL per loop iteration

```solidity
file:   contracts/core/Vault.sol

169         for (uint256 _i; _i < _tokenAddresses.length;) {
      IVaultController.CollateralInfo memory _collateralInfo = CONTROLLER.tokenCollateralInfo(_tokenAddresses[_i]);
      if (_collateralInfo.tokenId == 0) revert Vault_TokenNotRegistered();
      if (_collateralInfo.collateralType != IVaultController.CollateralType.CurveLPStakedOnConvex) {
        revert Vault_TokenNotCurveLP();
      }

188           for (uint256 _j; _j < _extraRewards;) {
        IVirtualBalanceRewardPool _virtualReward = _rewardsContract.extraRewards(_j);
        IERC20 _rewardToken = _virtualReward.rewardToken();
        uint256 _earnedReward = _virtualReward.earned(address(this));
        if (_earnedReward != 0) {
          _virtualReward.getReward();
          _rewardToken.transfer(_msgSender(), _earnedReward);
          emit ClaimedReward(address(_rewardToken), _earnedReward);
        }
        unchecked {
          ++_j;
        }
      }
      unchecked {
        ++_i;
      }
    }  

254        for (_i; _i < _rewardsAmount;) {
      IVirtualBalanceRewardPool _virtualReward = _rewardsContract.extraRewards(_i);
      IERC20 _rewardToken = _virtualReward.rewardToken();
      uint256 _earnedReward = _virtualReward.earned(address(this));
      _rewards[_i + 2] = Reward(_rewardToken, _earnedReward);

      unchecked {
        ++_i;
      }
    }
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L169-L175


### Cache IVaultController(_vaultControllers.at(_i)).calculateInterest(); outside of loop to save 1 STATICCALL per loop iteration

```solidity
file:  contracts/core/USDA.sol

48   for (uint256 _i; _i < _vaultControllers.length();) {
      IVaultController(_vaultControllers.at(_i)).calculateInterest();
      unchecked {
        _i++;
      }
    }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L48-L53

### Cache _oldVaultController.tokenCollateralInfo(_tokenAddresses[_i]); outside of loop to save 1 STATICCALL per loop iteration

```solidity
file:   contracts/core/VaultController.sol

255         for (uint256 _i; _i < _tokenAddresses.length;) {
      _tokenId = _oldVaultController.tokenId(_tokenAddresses[_i]);
      if (_tokenId == 0) revert VaultController_WrongCollateralAddress();
      _tokensRegistered++;

      CollateralInfo memory _collateral = _oldVaultController.tokenCollateralInfo(_tokenAddresses[_i]);
      _collateral.tokenId = _tokensRegistered;
      _collateral.totalDeposited = 0;

      enabledTokens.push(_tokenAddresses[_i]);
      tokenAddressCollateralInfo[_tokenAddresses[_i]] = _collateral;

      unchecked {
        ++_i;
      }
    }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L255-L270


## [G-02] Combine events to save 2 Glogtopic (375 gas)

The events below are only emitted once in the handleRewards function. We can combine the events into one singular event to save two Glogtopic (375 gas) that would otherwise be paid for the additional two events.

```solidity
file:     contracts/governance/GovernorCharlie.sol

277        emit ProposalQueuedIndexed(_proposalId, _eta);
278        emit ProposalQueued(_proposalId, _eta);

318        emit ProposalExecutedIndexed(_proposalId);
319        emit ProposalExecuted(_proposalId);

388        emit ProposalCanceledIndexed(_proposalId);
389        emit ProposalCanceled(_proposalId);

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L277-L278

```solidity
file:      contracts/governance/GovernorCharlie.sol

487       emit VoteCastIndexed(msg.sender, _proposalId, _support, _numberOfVotes, '');
488       emit VoteCast(msg.sender, _proposalId, _support, _numberOfVotes, '');

499       emit VoteCastIndexed(msg.sender, _proposalId, _support, _numberOfVotes, _reason);
500       emit VoteCast(msg.sender, _proposalId, _support, _numberOfVotes, _reason);

520       emit VoteCastIndexed(_signatory, _proposalId, _support, _numberOfVotes, '');
521       emit VoteCast(_signatory, _proposalId, _support, _numberOfVotes, '');

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L487-L488

## [G-03] Use calldata instead of memory for function arguments that do not get mutated

When you specify a data location as memory, that value will be copied into memory. When you specify the location as calldata, the value will stay static within calldata. If the value is a large, complex type, using memory may result in extra memory expansion costs.

```solidity

file:   contracts/core/Vault.sol

164    function claimRewards(address[] memory _tokenAddresses) external override onlyMinter

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164 

```solidity
file:    contracts/governance/GovernorCharlie.sol

159      function _propose(
    address[] memory _targets,
    uint256[] memory _values,
    string[] memory _signatures,
    bytes[] memory _calldatas,
    string memory _description,
    bool _emergency
  ) internal returns (uint256 _proposalId)

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L159-L166

```solidity
file:   solidity/interfaces/core/IVault.sol

193     function claimRewards(address[] memory _tokenAddresses) external;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVault.sol#L193

```solidity
file:   solidity/interfaces/governance/IGovernorCharlie.sol

164      function propose(
    address[] memory _targets,
    uint256[] memory _values,
    string[] memory _signatures,
    bytes[] memory _calldatas,
    string memory _description
  ) external returns (uint256 _proposalId);

181     function proposeEmergency(
    address[] memory _targets,
    uint256[] memory _values,
    string[] memory _signatures,
    bytes[] memory _calldatas,
    string memory _description
  ) external returns (uint256 _proposalId);

207     function executeTransaction(
    address _target,
    uint256 _value,
    string memory _signature,
    bytes memory _data,
    uint256 _eta
  ) external payable;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IGovernorCharlie.sol#L164-L170

```solidity
file:   solidity/interfaces/utils/ICurvePool.sol

11   function calc_token_amount(uint256[] memory _amounts, bool _deposit) external view returns (uint256 _amount);

16     function remove_liquidity(
    uint256 _amount,
    uint256[2] memory _minAmounts
  ) external returns (uint256[2] memory _amounts);

20    function add_liquidity(
    uint256[2] memory _amounts,
    uint256 _minMintAmount
  ) external payable returns (uint256 _lpAmount);

34   function remove_liquidity(uint256 _amount, uint256[2] memory _minAmounts, bool _useEth) external;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol#L11


## [G-04] Avoid emitting storage values

Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. We can avoid unecessary SLOADs by caching storage values that were previously accessed and emitting those cached values.


```solidity
file:  contracts/governance/GovernorCharlie.sol

587   emit NewDelay(_oldTimelockDelay, proposalTimelockDelay);

598   emit NewEmergencyDelay(_oldEmergencyTimelockDelay, emergencyTimelockDelay);

609   emit VotingDelaySet(_oldVotingDelay, votingDelay);

620   emit VotingPeriodSet(_oldVotingPeriod, votingPeriod);

631   emit EmergencyVotingPeriodSet(_oldEmergencyVotingPeriod, emergencyVotingPeriod); 

642   emit ProposalThresholdSet(_oldProposalThreshold, proposalThreshold);

653   emit NewQuorum(_oldQuorumVotes, quorumVotes);

664   emit NewEmergencyQuorum(_oldEmergencyQuorumVotes, emergencyQuorumVotes);

688   emit WhitelistGuardianSet(_oldGuardian, whitelistGuardian);

699   emit OptimisticVotingDelaySet(_oldOptimisticVotingDelay, optimisticVotingDelay);

710   emit OptimisticQuorumVotesSet(_oldOptimisticQuorumVotes, optimisticQuorumVotes);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L587

```solidity
file:  contracts/utils/UFragments.sol

105    emit Transfer(address(this), address(0x0), _totalSupply);

286    emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);

301    emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L105

```solidity
file:  contracts/core/Vault.sol

150   emit Staked(_tokenAddress, balances[_tokenAddress]);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L150

```solidity
file:   contracts/core/VaultController.sol

290    emit NewVault(_vaultAddress, vaultsMinted, _msgSender());

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L290

## [G-05] A modifier used only once and not being inherited should be inlined to save gas

```solidity
file:  contracts/utils/UFragments.sol

51     modifier onlyMonetaryPolicy() {
    require(msg.sender == monetaryPolicy);
    _;
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L51


## [G-06] If statements that use && can be refactored into nested if statements

```solidity
file:  contracts/core/AMPHClaimer.sol

88   if (_crvAmountToSend != 0 && _claimedAmph != 0) distributedAmph += (_claimedAmph / 1e12); 

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L88

```solidity
file:  contracts/core/Vault.sol

158   if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L158

```solidity
file:   contracts/core/VaultController.sol

1018    if (_increase && (_collateral.totalDeposited + _amount) > _collateral.cap) revert VaultController_CapReached();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L1018

```solidity
file:   contracts/governance/GovernorCharlie.sol

170    if (amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender))

208   if (_emergency && !isWhitelisted(msg.sender))

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L170

```solidity
file:   contracts/utils/ThreeLines0_100.sol

34      if (!((0 < _r2) && (_r2 < _r1) && (_r1 < _r0))) revert ThreeLines0_100_InvalidCurve();
35      if (!((0 < _s1) && (_s1 < _s2) && (_s2 < 1e18))) revert ThreeLines0_100_InvalidBreakpointValues();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol#L34


## [G-07] Use assembly to write address storage values

In Ethereum, gas is a measure of computational effort required to perform certain operations on the blockchain. Gas costs are incurred when executing smart contracts or transactions on the Ethereum network. One way to save gas when dealing with address storage values is to use assembly code to optimize the operations.

In this example, let's assume you have a smart contract with a mapping that stores values for different Ethereum addresses. We will use assembly to write a function that stores a value for a given address in a gas-efficient manner.

Here's a simple Solidity smart contract with a mapping to store values:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract AddressStorage {
    mapping(address => uint256) public addressValues;

    function setAddressValue(address _address, uint256 _value) public {
        addressValues[_address] = _value;
    }
}
```
Now, let's implement an assembly function to set the value for a given address:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract AddressStorage {
    mapping(address => uint256) public addressValues;

    function setAddressValue(address _address, uint256 _value) public {
        setAddressValueGasEfficient(_address, _value);
    }

    function setAddressValueGasEfficient(address _address, uint256 _value) private {
        assembly {
            // Calculate the storage slot for the given address
            let slot := shr(96, _address)

            // Store the value in the computed slot
            sstore(slot, _value)
        }
    }
}

```
In this assembly code, we directly access the contract's storage to set the value for a specific address. The shr (shift right) instruction is used to convert the address to a storage slot, and then the sstore instruction is used to write the value to that slot. This way, we avoid using the mapping's set function and the associated overhead, saving gas in the process.


```solidity
file:  contracts/periphery/oracles/OracleRelay.sol

19    function _setUnderlying(address _underlying) internal {
    underlying = _underlying;
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L19-L21

```solidity
file:   contracts/core/USDA.sol

63     function setPauser(address _pauser) external override onlyOwner {
    pauser = _pauser;

297     function removeVaultControllerFromList(address _vaultController) external onlyOwner {
    _vaultControllers.remove(_vaultController);    

278     function addVaultController(address _vaultController) external onlyOwner {
    _vaultControllers.add(_vaultController);

287    function removeVaultController(address _vaultController) external onlyOwner {
    _vaultControllers.remove(_vaultController);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L63-L64

```solidity
file:  contracts/core/AMPHClaimer.sol

119      function changeVaultController(address _newVaultController) external override onlyOwner {
    vaultController = IVaultController(_newVaultController);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L119-L120

```solidity
file:   contracts/core/Vault.sol

284     function controllerTransfer(address _token, address _to, uint256 _amount) external override onlyVaultController {
    SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_token), _to, _amount);
    balances[_token] -= _amount;
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L284-L287

```solidity
file:  contracts/core/VaultController.sol

312     function registerCurveMaster(address _masterCurveAddress) external override onlyOwner {
    curveMaster = CurveMaster(_masterCurveAddress);

319     function changeProtocolFee(uint192 _newProtocolFee) external override onlyOwner {
    if (_newProtocolFee >= 1e18) revert VaultController_FeeTooLarge();
    protocolFee = _newProtocolFee;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L312-L313

```solidity
file:  contracts/governance/GovernorCharlie.sol

567    function setNewToken(address _token) external onlyGov {
    amph = IAMPH(_token);
  }

684     function setWhitelistGuardian(address _account) external override onlyGov {
    address _oldGuardian = whitelistGuardian;
    whitelistGuardian = _account;



```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L567-L569

```solidity
file:   contracts/periphery/CurveMaster.sol
 
31     function setVaultController(address _vaultMasterAddress) external override onlyOwner {
    address _oldCurveAddress = vaultControllerAddress;
    vaultControllerAddress = _vaultMasterAddress;
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L33

```solidity
file:   contracts/periphery/oracles/CTokenOracle.sol

57     function changeAnchoredView(address _anchoredViewUnderlying) external onlyOwner {
    anchoredViewUnderlying = IOracleRelay(_anchoredViewUnderlying);
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L57

```solidity
file:  contracts/utils/UFragments.sol

111   function setMonetaryPolicy(address _monetaryPolicy) external onlyOwner {
    monetaryPolicy = _monetaryPolicy;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L111

## [G‑08] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

```solidity
file:

210  while (_tempAmountReceived > 0) 

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L210

```solidity
file:  contracts/core/Vault.sol

206    if (_totalCrvReward > 0 || _totalCvxReward > 0)

223    if (_totalCvxReward > 0) CVX.transfer(_msgSender(), _totalCvxRewar

224    if (_totalCrvReward > 0) CRV.transfer(_msgSender(), _totalCrvReward);

276    if (_cvxReward > 0) _rewards[1].amount = _cvxReward - _takenCVX;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L206

```solidity
file:  contracts/core/VaultController.sol

558    if (_fee > 0) usda.vaultControllerMint(owner(), _fee);

660    if (_tokenAmount > 0) _tokensToLiquidate = _tokenAmount;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L558

```solidity
file:  contracts/periphery/oracles/AnchoredViewRelay.sol

77     require(_mainValue > 0, 'invalid oracle value');

80     require(_anchorPrice > 0, 'invalid anchor value');

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L77

## [G-09] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

```solidity
file:  contracts/core/AMPHClaimer.sol

18    uint256 internal constant _FIFTY_MILLIONS = 50_000_000 * 1e6;

21    uint256 internal constant _TWENTY_FIVE_THOUSANDS = 25_000 * 1e6;

24    uint256 internal constant _FIFTY = 50 * 1e6;

27    uint256 public constant BASE_SUPPLY_PER_CLIFF = 8_000_000 * 1e6;
 
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L18

```solidity
file:  contracts/core/WUSDA.sol

35     uint256 public constant MAX_wUSDA_SUPPLY = 10_000_000 * (10 ** 18); // 10 M

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L35

```solidity
file:   contracts/utils/UFragments.sol

61      uint256 private constant DECIMALS = 18;
62      uint256 private constant MAX_UINT256 = 2 ** 256 - 1;
53      uint256 private constant INITIAL_FRAGMENTS_SUPPLY = 1 * 10 ** DECIMALS;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L61

## [G-10] Use hardcode address instead address(this)

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. 

```solidity
file:  contracts/core/AMPHClaimer.sol

166    uint256 _amphBalance = AMPH.balanceOf(address(this));

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L166

```solidity
file:  contracts/core/USDA.sol

103    sUSD.transferFrom(_msgSender(), address(this), _susdAmount);

206    sUSD.transferFrom(_msgSender(), address(this), _susdAmount);

215    uint256 _amount = sUSD.balanceOf(address(this)) - reserveAmount;


```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L103 

```solidity
file:  contracts/core/Vault.sol

92    SafeERC20Upgradeable.safeTransferFrom(IERC20Upgradeable(_token), _msgSender(), address(this), _amount);

177   uint256 _crvReward = _rewardsContract.earned(address(this));

182   _rewardsContract.getReward(address(this), false);

191   uint256 _earnedReward = _virtualReward.earned(address(this));

210    _amphClaimer.claimable(address(this), this.id(), _totalCvxReward, _totalCrvReward);

245    uint256 _crvReward = _rewardsContract.earned(address(this));

257    uint256 _earnedReward = _virtualReward.earned(address(this));

271    (_takenCVX, _takenCRV, _claimableAmph) = _amphClaimer.claimable(address(this), this.id(), _cvxReward, _crvReward);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L177

```solidity
file:  contracts/core/WUSDA.sol

215    IERC20(USDA).safeTransferFrom(_from, address(this), _usdaAmount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L215

```solidity
file:  contracts/governance/GovernorCharlie.sol

107    if (msg.sender != address(this)) revert GovernorCharlie_NotGovernorCharlie();

335    if (msg.sender != address(this)) revert GovernorCharlie_NotGovernorCharlie();

509    keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(NAME)), _getChainIdInternal(), address(this)));

716      _timelock = address(this);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L107

```solidity
file:  contracts/utils/UFragments.sol

57    if (_to == address(0) || _to == address(this)) revert UFragments_InvalidRecipient();

105   emit Transfer(address(this), address(0x0), _totalSupply);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L57

## [G‑11] Don’t compare boolean expressions to boolean literals

if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

There are 6 instances of this issue:

```solidity
file:  contracts/core/Vault.sol

102    isTokenStaked[_token] = true;

145    isTokenStaked[_tokenAddress] = true;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L102


```solidity
file:  contracts/governance/GovernorCharlie.sol

300    queuedTransactions[_txHash] = true;

312    _proposal.executed = true;

342     queuedTransactions[_txHash] = false;

406     queuedTransactions[_txHash] = false;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L300

## [G-12] Assigning keccak operations to constant variables results in extra gas costs

"constants" expressions are expressions. As such, keccak assigned to a constant variable are re-computed at each use of the variable, which will consume gas unnecessarily. To save gas, Changing the variable from constant to immutable will make the computation run only once and therefore save gas.

```solidity
file:  contracts/core/USDA.sol

21     bytes32 public constant VAULT_CONTROLLER_ROLE = keccak256('VAULT_CONTROLLER');

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L21

```solidity
file:  contracts/governance/GovernorCharlie.sol

19     bytes32 public constant DOMAIN_TYPEHASH =
    keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

23     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L19-L20

```solidity
file:  contracts/utils/UFragments.sol

87    bytes32 public constant EIP712_DOMAIN =
    keccak256('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)');

89     bytes32 public constant PERMIT_TYPEHASH =
    keccak256('Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)');

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L87-L88

## [G-13] With assembly, .call (bool success) transfer can be done gas-optimized

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.

```solidity
file:   contracts/governance/GovernorCharlie.sol

350     (bool _success, /*bytes memory returnData*/ ) = _target.call{value: _value}(_callData);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L350


## [G-14] Unnecessary look up in if condition

If the || condition isn’t required, the second condition will have been looked up unnecessarily.


```solidity
file:  contracts/core/Vault.sol

206    if (_totalCrvReward > 0 || _totalCvxReward > 0)

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L206

```solidity
file:  contracts/core/AMPHClaimer.sol

169    if (_crvTotalRewards == 0 || _amphBalance == 0) return (0, 0, 0);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L169

## [G‑15] State variables can be packed into fewer storage slots

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper.

```solidity
file:   contracts/core/VaultController.sol

25      uint8 public constant MAX_DECIMALS = 18;

  /// @dev The max allowed to be set as borrowing fee
  uint192 public constant MAX_INIT_BORROWING_FEE = 0.05e18;

  /// @dev The convex booster interface
  IBooster public immutable BOOSTER;

  /// @dev The vault deployer interface
  IVaultDeployer public immutable VAULT_DEPLOYER;

  /// @dev Mapping of vault id to vault address
  mapping(uint96 => address) public vaultIdVaultAddress;

  /// @dev Mapping of wallet address to vault IDs arrays
  mapping(address => uint96[]) public walletVaultIDs;

  /// @dev Mapping of token address to collateral info
  mapping(address => CollateralInfo) public tokenAddressCollateralInfo;

  /// @dev Array of enabled tokens addresses
  address[] public enabledTokens;

  /// @dev The curve master contract
  CurveMaster public curveMaster;

  /// @dev The interest contract
  Interest public interest;

  /// @dev The usda interface
  IUSDA public usda;

  /// @dev The amphora claimer interface
  IAMPHClaimer public claimerContract;

  /// @dev Total number of minted vaults
  uint96 public vaultsMinted;
  /// @dev Total number of tokens registered
  uint256 public tokensRegistered;
  /// @dev Total base liability
  uint192 public totalBaseLiability;
  /// @dev The protocol's fee
  uint192 public protocolFee;
  /// @dev The initial borrowing fee (1e18 == 100%)
  uint192 public initialBorrowingFee;
  /// @dev The fee taken from the liquidator profit (1e18 == 100%)
  uint192 public liquidationFee;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L25-L71

```solidity
file:    contracts/periphery/oracles/UniswapV3OracleRelay.sol

17       bool public immutable QUOTE_TOKEN_IS_TOKEN0;

  /// @notice The pool
  IUniswapV3Pool public immutable POOL;

  /// @notice The lookback for the oracle
  uint32 public immutable LOOKBACK;

  /// @notice The base token decimals
  uint8 public immutable BASE_TOKEN_DECIMALS;

  /// @notice The quote token decimals
  uint8 public immutable QUOTE_TOKEN_DECIMALS;

  /// @notice The base token
  address public immutable BASE_TOKEN;

  /// @notice The quote token
  address public immutable QUOTE_TOKEN;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L17-L35

## [G-16] Use shift Right/Left instead of division/multiplication if possible


```solidity
file:  contracts/core/WUSDA.sol

35   uint256 public constant MAX_wUSDA_SUPPLY = 10_000_000 * (10 ** 18); // 10 M

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L35

```solidity
file:  contracts/periphery/oracles/CTokenOracle.sol

28   div = 10 ** (18 + _underlyingDecimals - cToken.decimals());

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L28

```solidity
file:  contracts/periphery/oracles/UniswapV3OracleRelay.sol

75    uint256 _uniswapPrice = _getPriceFromUniswap(_seconds, uint128(10 ** BASE_TOKEN_DECIMALS));

85   _e18Amount = (_decimals > 18) ? _amount / (10 ** (_decimals - 18)) : _amount * (10 ** (18 - _decimals));

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L75  

```solidity
file:  contracts/core/VaultController.sol

712    ) = _peekLiquidationMath(_id, _assetAddress, 2 ** 256 - 1);

1040   _priceWithDecimals = _price * 10 ** (18 - _decimals);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L712 

```solidity
file:   contracts/utils/UFragments.sol
 
62     uint256 private constant MAX_UINT256 = 2 ** 256 - 1;
63     uint256 private constant INITIAL_FRAGMENTS_SUPPLY = 1 * 10 ** DECIMALS;
100   _totalGons = INITIAL_FRAGMENTS_SUPPLY * 10 ** 48;
101    MAX_SUPPLY = 2 ** 128 - 1;

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L62

## [G-17] Amounts should be checked for 0 before calling a transfer

Checking non-zero transfer values can avoid an expensive external call and save gas.
While this is done at some places, it’s not consistently done in the solution.
I suggest adding a non-zero-value check here:


```solidity
file:    contracts/core/AMPHClaimer.sol

129       IERC20(_token).transfer(owner(), _amount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L129

```solidity
file:  contracts/core/USDA.sol

216    sUSD.transfer(_to, _amount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L216

```solidity
file:   contracts/core/VaultController.sol

551    usda.vaultControllerMint(_target, _amount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L551

```solidity
file:  contracts/core/WUSDA.sol

215   IERC20(USDA).safeTransferFrom(_from, address(this), _usdaAmount);

228   IERC20(USDA).safeTransfer(_to, _usdaAmount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L215



## [G‑18] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword.

```solidity
file:  contracts/periphery/oracles/OracleRelay.sol

19     function _setUnderlying(address _underlying) internal {


```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L19

```solidity
file:   contracts/periphery/oracles/ChainlinkStalePriceLib.sol

11     function getCurrentPrice(AggregatorV2V3Interface _aggregator) internal view returns (uint256 _price) 

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol#L11

```solidity
file:  contracts/periphery/oracles/CurveRegistryUtils.sol

12     function _getLpAddress(address _crvPool) internal view returns (address _lpAddress) 

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol#L12


## [G-19] Using a positive conditional flow to save a NOT opcode
Estimated savings: 3 gas

```solidity
file:  contracts/core/Vault.sol

127   if (!CONTROLLER.checkVault(vaultInfo.id)) revert Vault_OverWithdrawal();

120   if (!CONTROLLER.tokenCrvRewardsContract(_tokenAddress).withdrawAndUnwrap(_amount, false))

297   if (!_rewardPool.withdrawAndUnwrap(_amount, false)) revert Vault_WithdrawAndUnstakeOnConvexFailed();

322   if (!_booster.deposit(_poolId, _amount, true)) revert Vault_DepositAndStakeOnConvexFailed();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L127

```solidity
file:  contracts/governance/GovernorCharlie.sol

338   if (!queuedTransactions[_txHash]) revert GovernorCharlie_ProposalNotQueued();

351   if (!_success) revert GovernorCharlie_TransactionReverted();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L338

```solidity
file:   contracts/utils/ThreeLines0_100.sol

34      if (!((0 < _r2) && (_r2 < _r1) && (_r1 < _r0))) revert ThreeLines0_100_InvalidCurve();
35      if (!((0 < _s1) && (_s1 < _s2) && (_s2 < 1e18))) revert ThreeLines0_100_InvalidBreakpointValues();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol#L34

## [G-20] Use do while loops instead of for loops
A do while loop will cost less gas since the condition is not being checked for the first iteration.

```solidity
file:   contracts/core/VaultController.sol
contracts/utils/UFragments.sol
240       for (uint256 _i = _start; _i < _end;) {
      _collateralsInfo[_i - _start] = tokenAddressCollateralInfo[enabledTokens[_i]];

      unchecked {
        ++_i;
      }
    }
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L240

```solidity
file:   contracts/governance/GovernorCharlie.sol

382        for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {
      _cancelTransaction(
        _proposal.targets[_i], _proposal.values[_i], _proposal.signatures[_i], _proposal.calldatas[_i], _proposal.eta
      );
    }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L382

```solidity
file:    contracts/core/Vault.sol

188          for (uint256 _j; _j < _extraRewards;) {
        IVirtualBalanceRewardPool _virtualReward = _rewardsContract.extraRewards(_j);
        IERC20 _rewardToken = _virtualReward.rewardToken();
        uint256 _earnedReward = _virtualReward.earned(address(this));
        if (_earnedReward != 0) {
          _virtualReward.getReward();
          _rewardToken.transfer(_msgSender(), _earnedReward);
          emit ClaimedReward(address(_rewardToken), _earnedReward);
        }
        unchecked {
          ++_j;
        }
      }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L188-L200

```solidity
file:   solidity/contracts/core/USDA.sol

48        for (uint256 _i; _i < _vaultControllers.length();) {
      IVaultController(_vaultControllers.at(_i)).calculateInterest();
      unchecked {
        _i++;
      }
    }
    _;
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L48

```solidity
file:    contracts/core/Vault.sol

169       for (uint256 _i; _i < _tokenAddresses.length;) {
      IVaultController.CollateralInfo memory _collateralInfo = CONTROLLER.tokenCollateralInfo(_tokenAddresses[_i]);
      if (_collateralInfo.tokenId == 0) revert Vault_TokenNotRegistered();
      if (_collateralInfo.collateralType != IVaultController.CollateralType.CurveLPStakedOnConvex) {
        revert Vault_TokenNotCurveLP();
      }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L169-L175

## [G-21] Failure to check the zero address in the constructor causes the contract to be deployed again

Zero address control is not performed in the constructor in all 3 contracts within the scope of the audit. Bypassing this check could cause the contract to be deployed by mistakenly entering a zero address. In this case, the contract will need to be redeployed. This means extra gas consumption as contract deployment costs are high.



```solidity
file:  contracts/core/VaultController.sol

93     constructor(
    IVaultController _oldVaultController,
    address[] memory _tokenAddresses,
    IAMPHClaimer _claimerContract,
    IVaultDeployer _vaultDeployer,
    uint192 _initialBorrowingFee,
    address _booster,
    uint192 _liquidationFee
  ) {
    VAULT_DEPLOYER = _vaultDeployer;
    interest = Interest(uint64(block.timestamp), 1 ether);
    protocolFee = 1e14;
    initialBorrowingFee = _initialBorrowingFee;
    liquidationFee = _liquidationFee;

    claimerContract = _claimerContract;

    BOOSTER = IBooster(_booster);

    if (address(_oldVaultController) != address(0)) _migrateCollateralsFrom(_oldVaultController, _tokenAddresses);
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L93-L113

```solidity
file:  contracts/governance/AmphoraProtocolToken.sol

9     constructor(
    address _account,
    uint256 _initialSupply
  ) ERC20('Amphora Protocol', 'AMPH') ERC20Permit('Amphora Protocol') {
    if (_account == address(0)) revert AmphoraProtocolToken_InvalidAddress();
    if (_initialSupply <= 0) revert AmphoraProtocolToken_InvalidSupply();

    _mint(_account, _initialSupply);
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L9-L17

```solidity
file:  contracts/utils/ThreeLines0_100.sol

33   constructor(int256 _r0, int256 _r1, int256 _r2, int256 _s1, int256 _s2) {
    if (!((0 < _r2) && (_r2 < _r1) && (_r1 < _r0))) revert ThreeLines0_100_InvalidCurve();
    if (!((0 < _s1) && (_s1 < _s2) && (_s2 < 1e18))) revert ThreeLines0_100_InvalidBreakpointValues();

    R0 = _r0;
    R1 = _r1;
    R2 = _r2;
    S1 = _s1;
    S2 = _s2;
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol#L33-L42 


## [G‑22] Inverting the condition of an if-else-statement wastes gas
Flipping the true and false blocks instead saves 3 gas

```solidity
file:   contracts/core/USDA.sol

132    _susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance;

142     _susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L132

```solidity
file:  contracts/core/VaultController.sol

633    _collateralLiquidated = _tokenAmount != 0 ? _tokenAmount : _tokensToLiquidate;

1021   _increase ? _collateral.totalDeposited + _amount : _collateral.totalDeposited - _amount;
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L633

```solidity
file:  contracts/periphery/oracles/CTokenOracle.sol

25    uint256 _underlyingDecimals = cETH_ADDRESS == _cToken ? 18 : IERC20Metadata(cToken.underlying()).decimals();

31  address _underlying = cETH_ADDRESS == _cToken ? wETH_ADDRESS : cToken.underlying();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L25

```solidity
file:  contracts/periphery/oracles/UniswapV3OracleRelay.sol

85    _e18Amount = (_decimals > 18) ? _amount / (10 ** (_decimals - 18)) : _amount * (10 ** (18 - _decimals));

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L85


```solidity
file:  contracts/utils/UFragments.sol

299    _allowedFragments[msg.sender][_spender] = (_subtractedValue >= _oldValue) ? 0 : _oldValue - _subtractedValue;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L299

## [G-23] Use assembly to emit events

We can use assembly to emit events efficiently by utilizing scratch space and the free memory pointer. This will allow us to potentially avoid memory expansion costs.

Note: In order to do this optimization safely, we will need to cache and restore the free memory pointer.

To emit events in an Ethereum smart contract using assembly, you can use the log opcode. This allows you to save gas by directly using the low-level assembly language to emit events without relying on higher-level Solidity event syntax.

Here's an example of how to emit an event using assembly in a Solidity smart contract:

```solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract EventEmitter {
    event MyEvent(address indexed sender, uint256 indexed value);

    function emitEvent(uint256 _value) external {
        // Emitting the event using assembly
        assembly {
            // Calculate the size of the event data (address + uint256)
            let eventSize := 64
            
            // Allocate space for the event data
            let eventData := mload(0x40)
            
            // Store the sender's address in the first 32 bytes of the event data
            mstore(eventData, caller())
            
            // Store the value in the next 32 bytes of the event data
            mstore(add(eventData, 0x20), _value)
            
            // Emit the event
            log1(eventData, eventSize, 0x01)
        }
    }
}


```

```solidity
file:   contracts/utils/UFragments.sol

105     emit Transfer(address(this), address(0x0), _totalSupply);

113     emit LogMonetaryPolicyUpdated(_monetaryPolicy)

185      emit Transfer(msg.sender, _to, _value);

201      emit Transfer(msg.sender, _to, _value);

233      emit Transfer(_from, _to, _value); 

252     emit Transfer(_from, _to, _value);

271      emit Approval(msg.sender, _spender, _value);

286      emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);

301       emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);

339       emit Approval(_owner, _spender, _value);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L105


```solidity
file:   contracts/governance/GovernorCharlie.sol

225     emit ProposalCreatedIndexed

237     emit ProposalCreated

277    emit ProposalQueuedIndexed(_proposalId, _eta);

278    emit ProposalQueued(_proposalId, _eta);

307    emit QueueTransaction(_txHash, _target, _value, _signature, _data, _eta);

318    emit ProposalExecutedIndexed(_proposalId);

319    emit ProposalExecuted(_proposalId);

353    emit ExecuteTransaction(_txHash, _target, _value, _signature, _data, _eta);

388   emit ProposalCanceledIndexed(_proposalId);

389    emit ProposalCanceled(_proposalId);

408    emit CancelTransaction(_txHash, _target, _value, _signature, _data, _eta);

487    emit VoteCastIndexed(msg.sender, _proposalId, _support, _numberOfVotes, '');

488   emit VoteCast(msg.sender, _proposalId, _support, _numberOfVotes, '');

499        emit VoteCastIndexed(msg.sender, _proposalId, _support, _numberOfVotes, _reason);

500    emit VoteCast(msg.sender, _proposalId, _support, _numberOfVotes, _reason);

520        emit VoteCastIndexed(_signatory, _proposalId, _support, _numberOfVotes, '');

521     emit VoteCast(_signatory, _proposalId, _support, _numberOfVotes, '');

587     emit NewDelay(_oldTimelockDelay, proposalTimelockDelay);

598     emit NewEmergencyDelay(_oldEmergencyTimelockDelay, emergencyTimelockDelay);

609      emit VotingDelaySet(_oldVotingDelay, votingDelay);

620      emit VotingPeriodSet(_oldVotingPeriod, votingPeriod);

631       emit EmergencyVotingPeriodSet(_oldEmergencyVotingPeriod, emergencyVotingPeriod);

642     emit ProposalThresholdSet(_oldProposalThreshold, proposalThreshold);

653      emit NewQuorum(_oldQuorumVotes, quorumVotes);

664      emit NewEmergencyQuorum(_oldEmergencyQuorumVotes, emergencyQuorumVotes);

677       emit WhitelistAccountExpirationSet(_account, _expiration);

688    emit WhitelistGuardianSet(_oldGuardian, whitelistGuardian);

699     emit OptimisticVotingDelaySet(_oldOptimisticVotingDelay, optimisticVotingDelay);

710     emit OptimisticQuorumVotesSet(_oldOptimisticQuorumVotes, optimisticQuorumVotes);
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L225

```solidity
file:    contracts/core/VaultController.sol

273      emit CollateralsMigratedFrom(_oldVaultController, _tokenAddresses);

290      emit NewVault(_vaultAddress, vaultsMinted, _msgSender());

307      emit RegisterUSDA(_usdaAddress);

314     emit RegisterCurveMaster(_masterCurveAddress);

322     emit NewProtocolFee(_newProtocolFee);

411      emit UpdateRegisteredErc20(_tokenAddress, _ltv, _oracleAddress, _liquidationIncentive, _cap, _poolId);

420      emit ChangedClaimerContract(_oldClaimerContract, _newClaimerContract);

430       emit ChangedInitialBorrowingFee(_oldBorrowingFee, _newBorrowingFee);

440   emit ChangedLiquidationFee(_oldLiquidationFee, _newLiquidationFee);

561     emit BorrowUSDA(_id, address(_vault), _amount, _fee);

609     emit RepayUSDA(_id, address(_vault), _amountInUSDA);

694         emit Liquidate(_id, _assetAddress, _usdaToRepurchase, _tokensToLiquidate - _liquidationFee, _liquidationFee);

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L273

```solidity
file:    contracts/core/USDA.sol

108      emit Deposit(_target, _susdAmount);
156      emit Withdraw(_target, _susdAmount);
176      emit Transfer(address(0), _target, _amount);
177      emit Mint(_target, _amount);

196      emit Transfer(_target, address(0), _amount);
197      emit Burn(_target, _amount);

218      emit RecoveredDust(owner(), _amount);

248       emit VaultControllerTransfer(_target, _susdAmount);

263    emit Donation(_msgSender(), _amount, _totalSuppl

282     emit VaultControllerAdded(_vaultController);


291    emit VaultControllerRemoved(_vaultController);

300    emit VaultControllerRemovedFromList(_vaultController);

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L176


```solidity
file:  contracts/core/Vault.sol

108   emit Deposit(_token, _amount);

132   emit Withdraw(_tokenAddress, _amount);

150    emit Staked(_tokenAddress, balances[_tokenAddress]);

195   emit ClaimedReward(address(_rewardToken), _earnedReward);

226   emit ClaimedReward(address(CRV), _totalCrvReward);

227   emit ClaimedReward(address(CVX), _totalCvxReward);
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L108

```solidity
file:  contracts/periphery/CurveMaster.sol

35    emit VaultControllerSet(_oldCurveAddress, _vaultMasterAddress);

46    emit CurveSet(_oldCurve, _tokenAddress, _curveAddress);

56    emit CurveForceSet(_oldCurve, _tokenAddress, _curveAddress);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L35

## [G-24] Create immutable variable to avoid redundant external calls

In the instances below, an external call to retrieve the directory is performed each time _swap and _mint is invoked. We can avoid performing this call on each invocation by executing this external call once in the constructor and storing the directory as an immutable variable. Doing so will save 2 external calls each time didPay is called (didPay invokes _swap & _mint).

```solidity
file:  contracts/core/AMPHClaimer.sol

51     IVaultController public vaultController;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L51

## [G-25] Forgo internal function to save 1 STATICCALL

The _context internal function performs two external calls and returns both of the return values from those calls. Certain functions invoke _context but only use the return value from the first external call, thus performing an unnecessary extra external call. We can forgo using the internal function and instead only perform our desired external call to save 1 STATICCALL (100 gas).


```solidity
file:  contracts/periphery/oracles/ChainlinkOracleRelay.sol

58    (,,, uint256 _updatedAt,) = _AGGREGATOR.latestRoundData();  

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L58

```solidity
file:  contracts/core/VaultController.sol

631    (uint256 _tokenAmount, uint256 _badFillPrice) = _peekLiquidationMath(_id, _assetAddress, _tokensToLiquidate);

658    (uint256 _tokenAmount, uint256 _badFillPrice) = _liquidationMath(_id, _assetAddress, _tokensToLiquidate);


```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L631


## [G-26]| Use assembly for math (add, sub, mul, div)

When we say "use assembly for math operations," we mean that when performing basic math operations in a smart contract, it's more gas-efficient to use assembly code to perform the operation instead of Solidity's built-in math functions. This is because Solidity's built-in math functions require additional gas consumption to perform the operation.


```solidity
file:  contracts/periphery/oracles/AnchoredViewRelay.sol

91    uint256 _upperBounds = _anchorPrice + _buffer;

92    uint256 _lowerBounds = _anchorPrice - _buffer;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L91

```solidity
file:  contracts/periphery/oracles/CTokenOracle.sol

28     div = 10 ** (18 + _underlyingDecimals - cToken.decimals());

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L28

```solidity
file:   contracts/utils/ThreeLines0_100.sol 

86     _result = (_mE6 * _distance) / 1e6 + _b;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol#L86

```solidity
file:  contracts/core/AMPHClaimer.sol

198     _rate = 1e6 + (_foo - _bar);

225      uint256 _amphAvailableForThisCliff = (_bottomLastCliff + BASE_SUPPLY_PER_CLIFF) - _distributedAmph; // 1e6

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L198


```solidity
file:  contracts/core/VaultController.sol

357    tokensRegistered = tokensRegistered + 1;

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L357

```solidity
file:   contracts/governance/GovernorCharlie.sol
 
195    startBlock: block.number + votingDelay,

196    endBlock: block.number + votingDelay + votingPeriod,

258    uint256 _eta = block.timestamp + _proposal.delay;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L195

## [G-27] Use assembly to validate msg.sender

We can use assembly to efficiently validate msg.sender for the didPay and uniswapV3SwapCallback functions with the least amount of opcodes necessary. Additionally, we can use xor() instead of iszero(eq()), saving 3 gas. We can also potentially save gas on the unhappy path by using scratch space to store the error selector, potentially avoiding memory expansion costs.

```solidity
file:   contracts/governance/GovernorCharlie.sol

107     if (msg.sender != address(this)) revert GovernorCharlie_NotGovernorCharlie();

335      if (msg.sender != address(this)) revert GovernorCharlie_NotGovernorCharlie();

367     if (msg.sender != _proposal.proposer)

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L107

## [G-28] Use lock_index to look for empty elements
Gas saved: 2.2K * (amount of non-empty elements)