# Gas Optimization

# Summary

| Number | Optimization Details                                                                                       | Context |
| :----: | :--------------------------------------------------------------------------------------------------------- | :-----: |
| [G-01] | Gas ineﬃcient implementation of a loop in `VaultController::_getVaultBorrowingPower()`                     |    1    |
| [G-02] | Before some functions we should `check` some variables for possible gas save                               |    7    |
| [G-03] | Internal functions not called by the contract should be `removed` to save deployment gas                   |    3    |
| [G-04] | Make 3 `event` parameters indexed when possible                                                            |   32    |
| [G-05] | Use assembly to write address `storage` values                                                             |   10    |
| [G-06] | Use nested if statements instead of `&&`                                                                   |    7    |
| [G-07] | `keccak256()` should only need to be called on a specific string literal once                              |    4    |
| [G-08] | Should use `arguments` instead of state variables in `emit`                                                |   16    |
| [G-09] | Using `XOR (^)` and `OR ` bitwise equivalents                                                              |    7    |
| [G-10] | Duplicated `if()` checks should be refactored to a modifier or function                                    |   17    |
| [G-11] | Do not calculate constants                                                                                 |    8    |
| [G-12] | Use hardcode address instead `address(this)`                                                               |    8    |
| [G-13] | Not using the named return variables when a function returns, wastes deployment gas                        |    6    |
| [G-14] | Can Make The Variable Outside The `Loop` To Save Gas                                                       |   14    |
| [G-15] | External call cost is less expensive than of public functions                                              |    9    |
| [G-16] | Expressions for constant values such as a call to `keccak256()`, should use immutable rather than constant |    5    |
| [G-17] | Use `!= 0` instead of `> 0` for unsigned integer comparison                                                |    8    |
| [G-18] | State variable can be packed into fewer `storage` slots                                                    |    1    |
| [G-19] | Use nested if and, avoid `multiple` check combinations                                                     |    7    |
| [G-20] | Non efficient zero initialization                                                                          |    3    |
| [G-21] | Variable names that consist of all capital letters should be reserved for constant/immutable variables     |    1    |

## [G-01] Gas ineﬃcient implementation of a loop in `VaultController::_getVaultBorrowingPower()`

`_getVaultBorrowingPower()` may loop through a large number of array elements, calling external functions on every single one, leading to signiﬁcant gas costs.

`_getVaultBorrowingPower()` contains a loop, which loops through all of the registered tokens
and queries the vault for its balance through a call to `_vault.balances()` which in turn makes the external call .

It also contains external calls to external oracles for each registered token through `_collateral.oracle.currentValue()`.

In a situation where the protocol expands to support a large number of tokens, calls to `_getVaultBorrowingPower()` could become gas heavy, especially as they contain external calls to code outside of the protocol’s control.

This could impact the cost and operation of the protocol.

### Recommendations

This issue can be partially mitigated by carefully managing and curating the list of registered tokens and oracles. However, this does not completely mitigate the issue To fully address this issue, more signiﬁcant changes may be required.

One approach would be to make all vaults single asset. That way, there is no need for the loop.

Another approach would be for vaults to track their token balances internally and return these internally stored values when `_vault.balances()` is called, although this does not address issues with oracle calls.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L866-L888

```solidity
File: contracts/core/VaultController.sol

  function _getVaultBorrowingPower(IVault _vault) private returns (uint192 _borrowPower) {
    // loop over each registed token, adding the indivuduals ltv to the total ltv of the vault
    for (uint192 _i; _i < enabledTokens.length; ++_i) {
      CollateralInfo memory _collateral = tokenAddressCollateralInfo[enabledTokens[_i]];
      // if the ltv is 0, continue
      if (_collateral.ltv == 0) continue;
      // get the address of the token through the array of enabled tokens
      // note that index 0 of enabledTokens corresponds to a vaultId of 1, so we must subtract 1 from i to get the correct index
      address _tokenAddress = enabledTokens[_i];
      // the balance is the vault's token balance of the current collateral token in the loop
      uint256 _balance = _vault.balances(_tokenAddress);
      if (_balance == 0) continue;
      // the raw price is simply the oracle price of the token
      uint192 _rawPrice = _safeu192(_collateral.oracle.currentValue());
      if (_rawPrice == 0) continue;
      // the token value is equal to the price * balance * tokenLTV
      uint192 _tokenValue = _safeu192(
        _truncate(_truncate(_balance * _collateral.ltv * _getPriceWithDecimals(_rawPrice, _collateral.decimals)))
      );
      // increase the ltv of the vault by the token value
      _borrowPower += _tokenValue;
    }
  }
```

## [G-02] Before some functions we should `check` some variables for possible gas save

Before transfer, we should check for `_amount` being 0 so the function doesn't run when its not gonna do anything:

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L29

```solidity
File:  core/solidity/contracts/core/AMPHClaimer.sol

129       IERC20(_token).transfer(owner(), _amount);

```

Before transfer, we should check for `_amount` and `_susdAmount` being 0 so the function doesn't run when its not gonna do anything:

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol

```solidity
File:   core/solidity/contracts/core/USDA.sol

216    sUSD.transfer(_to, _amount);

246    sUSD.transfer(_target, _susdAmount);
```

Before transfer, we should check for `_amount` being 0 so the function doesn't run when its not gonna do anything:

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol

```solidity
File:   core/solidity/contracts/core/Vault.sol

129       SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_tokenAddress), _msgSender(), _amount);

285         SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_token), _to, _amount);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol

Before transfer, we should check for `_usdaAmount` being 0 so the function doesn't run when its not gonna do anything:

```solidity
File:  core/solidity/contracts/core/WUSDA.sol

215     IERC20(USDA).safeTransferFrom(_from, address(this), _usdaAmount);

228     IERC20(USDA).safeTransfer(_to, _usdaAmount);
```

## [G-03] internal functions not called by the contract should be `removed` to save deployment gas

When deploying a smart contract, it is essential to optimize gas usage to minimize transaction costs and improve overall efficiency. One aspect to consider is removing unused internal functions that are not called by the contract. By eliminating these unused functions, you can significantly reduce the deployment gas cost.

Internal functions in Solidity are only intended to be invoked within the contract or by other internal functions. If an internal function is not called anywhere within the contract, it serves no purpose and contributes unnecessary overhead during deployment. Removing such functions can lead to substantial gas savings.

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol#L12

```solidity
File:   core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol

12     function _getLpAddress(address _crvPool) internal view returns (address _lpAddress) {
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol#L48

```solidity
File:  core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol

48   function _getVirtualPrice() internal view override returns (uint256 _value) {
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L19

```solidity
File:  core/solidity/contracts/periphery/oracles/OracleRelay.sol

19     function _setUnderlying(address _underlying) internal {
```

## [G-04] Make 3 `event` parameters indexed when possible

events are used to emit information about state changes in a contract. When defining an event, it's important to consider the gas cost of emitting the event, as well as the efficiency of searching for events.

Making event parameters indexed can help reduce the gas cost of emitting events and improve the efficiency of searching for events.

When an event parameter is marked as indexed, its value is stored in a separate data structure called the event topic, which allows for more efficient searching of events.

Exmple:

```
• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes value);

• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes indexed value);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol

```solidity
File:  core/solidity/contracts/utils/UFragments.sol

39  event LogRebase(uint256 indexed epoch, uint256 totalSupply);

40  event LogMonetaryPolicyUpdated(address monetaryPolicy);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IAMPHClaimer.sol

```solidity
File: core/solidity/interfaces/core/IAMPHClaimer.sol

20    event ClaimedAmph(
21    address indexed _vaultClaimer, uint256 _cvxTotalRewards, uint256 _crvTotalRewards, uint256 _amphAmount
22  );


36   event RecoveredDust(address indexed _token, address _receiver, uint256 _amount);

42    event ChangedCvxRewardFee(uint256 _newCvxReward);

48     event ChangedCrvRewardFee(uint256 _newCrvReward);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IUSDA.sol

```solidity
File:  core/solidity/interfaces/core/IUSDA.sol

19    event Deposit(address indexed _from, uint256 _value);

26      event Withdraw(address indexed _from, uint256 _value);

33    event Mint(address _to, uint256 _value);

40     event Burn(address _from, uint256 _value);

48      event Donation(address indexed _from, uint256 _value, uint256 _totalSupply);

55      event RecoveredDust(address indexed _receiver, uint256 _amount);

68   event VaultControllerTransfer(address _target, uint256 _susdAmount);

```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IVault.sol

```solidity
File:  core/solidity/interfaces/core/IVault.sol

20     event Deposit(address _token, uint256 _amount);

27       event Withdraw(address _token, uint256 _amount);

34     event ClaimedReward(address _token, uint256 _amount);

41      event Staked(address _token, uint256 _amount);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IVaultController.sol

```solidity
File:  core/solidity/interfaces/core/IVaultController.sol

23    event InterestEvent(uint64 _epoch, uint192 _amount, uint256 _curveVal);

29     event NewProtocolFee(uint192 _protocolFee);

39     event RegisteredErc20(
40    address _tokenAddress, uint256 _ltv, address _oracleAddress, uint256 _liquidationIncentive, uint256 _cap
41  );


52    event UpdateRegisteredErc20(
53    address _tokenAddress,
54    uint256 _ltv,
55    address _oracleAddress,
56    uint256 _liquidationIncentive,
57    uint256 _cap,
58    uint256 _poolId
59  );


67    event NewVault(address _vaultAddress, uint256 _vaultId, address _vaultOwner);

73     event RegisterCurveMaster(address _curveMasterAddress);

81     event BorrowUSDA(uint256 _vaultId, address _vaultAddress, uint256 _borrowAmount, uint256 _fee);

89     event RepayUSDA(uint256 _vaultId, address _vaultAddress, uint256 _repayAmount);


99     event Liquidate(
100    uint256 _vaultId,
101    address _assetAddress,
102    uint256 _usdaToRepurchase,
103    uint256 _tokensToLiquidate,
104    uint256 _liquidationFee
105  );


118    event RegisterUSDA(address _usdaContractAddress);


125     event ChangedInitialBorrowingFee(uint192 _oldBorrowingFee, uint192 _newBorrowingFee);


132     event ChangedLiquidationFee(uint192 _oldLiquidationFee, uint192 _newLiquidationFee);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/periphery/ICurveMaster.sol

```solidity
File:  core/solidity/interfaces/periphery/ICurveMaster.sol

16     event VaultControllerSet(address _oldVaultControllerAddress, address _newVaultControllerAddress);

24      event CurveSet(address _oldCurveAddress, address _token, address _newCurveAddress);

32      event CurveForceSet(address _oldCurveAddress, address _token, address _newCurveAddress);
```

## [G-05] Use assembly to write address `storage` values

By using assembly to write to address storage values, you can bypass some of these operations and lower the gas cost of writing to storage. Assembly code allows you to directly access the Ethereum Virtual Machine (EVM) and perform low-level operations that are not possible in Solidity.

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol

```solidity
File:  core/solidity/contracts/core/AMPHClaimer.sol

61    vaultController = IVaultController(_vaultController);

120   vaultController = IVaultController(_newVaultController);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L64

```solidity
File:  core/solidity/contracts/core/USDA.sol
64   pauser = _pauser;
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L49

```solidity
File:  core/solidity/contracts/core/WUSDA.sol
49   USDA = _usdaToken;
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L44

```solidity
File:  core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol

44    anchorRelay = IOracleRelay(_anchorAddress);

46     mainRelay = IOracleRelay(_mainAddress);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L40

```solidity
File:  core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol

40   _AGGREGATOR = AggregatorV2V3Interface(_feedAddress);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L21

```solidity
File:  core/solidity/contracts/periphery/oracles/CTokenOracle.sol

21   cToken = ICToken(_cToken);

58    anchoredViewUnderlying = IOracleRelay(_anchoredViewUnderlying);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L42

```solidity
File:  core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol

42    LOOKBACK = _lookback;
```

## [G-06] Use nested if statements instead of `&&`

If the if statement has a logical AND and is not followed by an else statement, it can be replaced with 2 if statements.

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L88

```solidity
File:  core/solidity/contracts/core/AMPHClaimer.sol

88    if (_crvAmountToSend != 0 && _claimedAmph != 0) distributedAmph += (_claimedAmph / 1e12); // scale back to 1e6
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L158

```solidity
File:  core/solidity/contracts/core/Vault.sol

158     if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L1018

```solidity
File:  core/solidity/contracts/core/VaultController.sol

1018    if (_increase && (_collateral.totalDeposited + _amount) > _collateral.cap) revert VaultController_CapReached();
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol

170    if (amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender)) {

208    if (_emergency && !isWhitelisted(msg.sender)) {
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/ThreeLines0_100.sol

```solidity
File:  core/solidity/contracts/utils/ThreeLines0_100.sol

34   if (!((0 < _r2) && (_r2 < _r1) && (_r1 < _r0))) revert ThreeLines0_100_InvalidCurve();

35   if (!((0 < _s1) && (_s1 < _s2) && (_s2 < 1e18))) revert ThreeLines0_100_InvalidBreakpointValues();
```

## [G-07] `keccak256()` should only need to be called on a specific string literal once

It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to bytes4 should also only be done once.

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
File:   core/solidity/contracts/governance/GovernorCharlie.sol

19     bytes32 public constant DOMAIN_TYPEHASH =
20    keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');


23      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol

```solidity
File:  core/solidity/contracts/utils/UFragments.sol

87    bytes32 public constant EIP712_DOMAIN =
88    keccak256('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)');


89     bytes32 public constant PERMIT_TYPEHASH =
90    keccak256('Permit(address owner,address spender,uint256 value,uint256 nnoce,uint256 deadline)');
```

## [G-08] Should use `arguments` instead of state variables in `emit`

state variables should not used in emit

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol

```solidity
File:  core/solidity/contracts/utils/UFragments.sol

105       emit Transfer(address(this), address(0x0), _totalSupply);

286       emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);

301       emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L150

```solidity
File:  core/solidity/contracts/core/Vault.sol

150       emit Staked(_tokenAddress, balances[_tokenAddress]);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L290

```solidity
File:   core/solidity/contracts/core/VaultController.sol

290      emit NewVault(_vaultAddress, vaultsMinted, _msgSender());
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L587

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol

587       emit NewDelay(_oldTimelockDelay, proposalTimelockDelay);

598       emit NewEmergencyDelay(_oldEmergencyTimelockDelay, emergencyTimelockDelay);

609       emit VotingDelaySet(_oldVotingDelay, votingDelay);

620       emit VotingPeriodSet(_oldVotingPeriod, votingPeriod);

631       emit EmergencyVotingPeriodSet(_oldEmergencyVotingPeriod, emergencyVotingPeriod);

642       emit ProposalThresholdSet(_oldProposalThreshold, proposalThreshold);

653       emit NewQuorum(_oldQuorumVotes, quorumVotes);

664      emit NewEmergencyQuorum(_oldEmergencyQuorumVotes, emergencyQuorumVotes);

688      emit WhitelistGuardianSet(_oldGuardian, whitelistGuardian);

699      emit OptimisticVotingDelaySet(_oldOptimisticVotingDelay, optimisticVotingDelay);

710      emit OptimisticQuorumVotesSet(_oldOptimisticQuorumVotes, optimisticQuorumVotes);
```

## [G-09] Using `XOR (^)` and `OR (|)` bitwise equivalents

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L169

```solidity
File:  core/solidity/contracts/core/Vault.sol

169      if (_crvTotalRewards == 0 || _amphBalance == 0) return (0, 0, 0);
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L206

```solidity
File:  core/solidity/contracts/core/Vault.sol

206   if (_totalCrvReward > 0 || _totalCvxReward > 0) {
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
174     _targets.length != _values.length || _targets.length != _signatures.length || _targets.length != _calldatas.length

372      || msg.sender != whitelistGuardian

463     if (proposalCount < _proposalId || _proposalId <= initialProposalId) revert GovernorCharlie_InvalidProposalId();

471    || (!_whitelisted && _proposal.forVotes <= _proposal.againstVotes)
        || (!_whitelisted && _proposal.forVotes < _proposal.quorumVotes)
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L41

```solidity
File:  core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol

41       _stale = AGGREGATOR.isStale() || BASE_AGGREGATOR.isStale();
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L57

```solidity
File:  core/solidity/contracts/utils/UFragments.sol

57       if (_to == address(0) || _to == address(this)) revert UFragments_InvalidRecipient();
```

## [G-10] Duplicated `if()` checks should be refactored to a modifier or function

Using modifiers or functions can make your code more gas-efficient by reducing the overall number of operations that need to be executed. For example, if you have a complex validation check that involves multiple operations, and you refactor it into a function, then the function can be executed with a single opcode, rather than having to execute each operation separately in multiple locations.

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol

```solidity
File:    core/solidity/contracts/core/USDA.sol

102       if (_susdAmount == 0) revert USDA_ZeroAmount();

149       if (_susdAmount == 0) revert USDA_ZeroAmount();

162       if (_susdAmount == 0) revert USDA_ZeroAmount();

183       if (_susdAmount == 0) revert USDA_ZeroAmount();

203       if (_susdAmount == 0) revert USDA_ZeroAmount();
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol

```solidity
file: /contracts/core/VaultController.sol

355    if (_ltv >= (EXP_SCALE - _liquidationIncentive)) revert VaultController_LTVIncompatible();

394    if (_ltv >= (EXP_SCALE - _liquidationIncentive)) revert VaultController_LTVIncompatible();



727    if (peekCheckVault(_id)) revert VaultController_VaultSolvent();

817    if (peekCheckVault(_id)) revert VaultController_VaultSolvent();



808    if (_vaultAddress == address(0)) revert VaultController_VaultDoesNotExist();

848    if (_vaultAddress == address(0)) revert VaultController_VaultDoesNotExist();



392     if (_collateral.tokenId == 0) revert VaultController_TokenNotRegistered();

1017    if (_collateral.tokenId == 0) revert VaultController_TokenNotRegistered();

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
file: /contracts/governance/GovernorCharlie.sol

107    if (msg.sender != address(this)) revert GovernorCharlie_NotGovernorCharlie();

335    if (msg.sender != address(this)) revert GovernorCharlie_NotGovernorCharlie();

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol

```solidity
file: /core/solidity/contracts/core/Vault.sol

118    if (CONTROLLER.tokenId(_tokenAddress) == 0) revert Vault_TokenNotRegistered();

235    if (CONTROLLER.tokenId(_tokenAddress) == 0) revert Vault_TokenNotRegistered();

```

## [G-11] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol

```solidity
File:  core/solidity/contracts/core/AMPHClaimer.sol

18     uint256 internal constant _FIFTY_MILLIONS = 50_000_000 * 1e6;

21     uint256 internal constant _TWENTY_FIVE_THOUSANDS = 25_000 * 1e6;

24     uint256 internal constant _FIFTY = 50 * 1e6;

27     uint256 public constant BASE_SUPPLY_PER_CLIFF = 8_000_000 * 1e6;

30     uint256 public constant TOTAL_CLIFFS = 1000;
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L35

```solidity
File:   core/solidity/contracts/core/WUSDA.sol

35     uint256 public constant MAX_wUSDA_SUPPLY = 10_000_000 * (10 ** 18); // 10 M
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L62

```solidity
File:  core/solidity/contracts/utils/UFragments.sol

62   uint256 private constant MAX_UINT256 = 2 ** 256 - 1;

63   uint256 private constant INITIAL_FRAGMENTS_SUPPLY = 1 * 10 ** DECIMALS;
```

## [G-12] Use hardcode address instead `address(this)`

it can be more gas-efficient to use a hardcoded address instead of the address(this) expression, especially if you need to use the same address multiple times in your contract.

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol\

```solidity
File:   core/solidity/contracts/core/Vault.sol

92       SafeERC20Upgradeable.safeTransferFrom(IERC20Upgradeable(_token), _msgSender(), address(this), _amount);

177      uint256 _crvReward = _rewardsContract.earned(address(this));

182     _rewardsContract.getReward(address(this), false);

191      uint256 _earnedReward = _virtualReward.earned(address(this));

210     _amphClaimer.claimable(address(this), this.id(), _totalCvxReward, _totalCrvReward);

245      uint256 _crvReward = _rewardsContract.earned(address(this));

257      uint256 _earnedReward = _virtualReward.earned(address(this));

271      (_takenCVX, _takenCRV, _claimableAmph) = _amphClaimer.claimable(address(this), this.id(), _cvxReward, _crvReward);
```

## [G-13] Not using the named return variables when a function returns, wastes deployment gas

When a function returns multiple values without named return variables, it creates a temporary variable to hold the returned values, which can increase the deployment gas cost

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol

```solidity
File:   core/solidity/contracts/utils/UFragments.sol

121     return _totalSupply;

129     return _gonBalances[_who] / _gonsPerFragment;

137     return _gonBalances[_who];

145     return _totalGons;

154     return _nonces[_who];

168      return keccak256(
169      abi.encode(EIP712_DOMAIN, keccak256(bytes(name)), keccak256(bytes(EIP712_REVISION)), _chainId, address(this))
170    );

```

## [G-14] Can Make The Variable Outside The `Loop` To Save Gas

When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs, especially if the loop is executed frequently or iterates over a large number of elements.

By declaring the variable outside the loop, you can avoid the creation of multiple instances of the variable and reduce the gas cost of your contract. Here's an example:

```
contract MyContract {
    function sum(uint256[] memory values) public pure returns (uint256) {
        uint256 total = 0;

        for (uint256 i = 0; i < values.length; i++) {
            total += values[i];
        }

        return total;
    }
}
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol

```solidity
File:  core/solidity/contracts/core/Vault.sol

177   uint256 _crvReward = _rewardsContract.earned(address(this));

187   uint256 _extraRewards = _rewardsContract.extraRewardsLength();

209   (uint256 _takenCVX, uint256 _takenCRV, uint256 _claimableAmph) =
          _amphClaimer.claimable(address(this), this.id(), _totalCvxReward, _totalCrvReward);

257   uint256 _earnedReward = _virtualReward.earned(address(this));
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol

```solidity
File:  core/solidity/contracts/core/VaultController.sol

874   address _tokenAddress = enabledTokens[_i];

876   uint256 _balance = _vault.balances(_tokenAddress);

879   uint192 _rawPrice = _safeu192(_collateral.oracle.currentValue());

882   uint192 _tokenValue = _safeu192(

902    address _tokenAddress = enabledTokens[_i];

904    uint256 _balance = _vault.balances(_tokenAddress);

907    uint192 _rawPrice = _safeu192(_collateral.oracle.peekValue());

910    uint192 _tokenValue = _safeu192(

996    uint256[] memory _tokenBalances = new uint256[](enabledTokens.length);
```

## [G-15] External call cost is less expensive than of public functions

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol

```solidity
File:   core/solidity/contracts/core/VaultController.sol

278    function mintVault() public override whenNotPaused returns (address _vaultAddress) {

988   function vaultSummaries(
989    uint96 _start,
990    uint96 _stop
991  ) public view override returns (VaultSummary[] memory _vaultSummaries) {
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol


120    function propose(
121    address[] memory _targets,
122    uint256[] memory _values,
123    string[] memory _signatures,
124    bytes[] memory _calldatas,
125    string memory _description
126  ) public override returns (uint256 _proposalId) {



139    function proposeEmergency(
140    address[] memory _targets,
141    uint256[] memory _values,
142    string[] memory _signatures,
143    bytes[] memory _calldatas,
144    string memory _description
145  ) public override returns (uint256 _proposalId) {


583    function setDelay(uint256 _proposalTimelockDelay) public override onlyGov {


594    function setEmergencyDelay(uint256 _emergencyTimelockDelay) public override onlyGov {

```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol

```solidity
File:   core/solidity/contracts/utils/UFragments.sol

153     function nonces(address _who) public view returns (uint256 _addressNonces) {

283      function increaseAllowance(address _spender, uint256 _addedValue) public returns (bool _success) {


315    function permit(
316    address _owner,
317    address _spender,
318    uint256 _value,
319    uint256 _deadline,
320    uint8 _v,
321    bytes32 _r,
322    bytes32 _s
323  ) public {
```

## [G-16] Expressions for constant values such as a call to `keccak256()`, should use immutable rather than constant

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol

```solidity
File:  core/solidity/contracts/core/USDA.sol

21     bytes32 public constant VAULT_CONTROLLER_ROLE = keccak256('VAULT_CONTROLLER');
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
File:   core/solidity/contracts/governance/GovernorCharlie.sol

19   bytes32 public constant DOMAIN_TYPEHASH =
20    keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

23     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol

```solidity
File:  core/solidity/contracts/utils/UFragments.sol

87   bytes32 public constant EIP712_DOMAIN =
88    keccak256('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)');


89    bytes32 public constant PERMIT_TYPEHASH =
90    keccak256('Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)');
```

## [G-17] Use `!= 0` instead of `> 0` for unsigned integer comparison

it's generally more gas-efficient to use != 0 instead of > 0 when
comparing unsigned integer types.

This is because the Solidity compiler can optimize the != 0 comparison to a simple bitwise operation,
while the > 0 comparison requires an additional subtraction operation.
As a result, using != 0 can be more gas-efficient and can help to reduce the overall cost of your contract.

Here's an example of how you can use != 0 instead of > 0:

```
contract MyContract {
    uint256 public myUnsignedInteger;

    function myFunction() public view returns (bool) {
        // Use != 0 instead of > 0
        return myUnsignedInteger != 0;
    }
}
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol

```solidity
File:  core/solidity/contracts/core/Vault.sol

206    if (_totalCrvReward > 0 || _totalCvxReward > 0) {

223    if (_totalCvxReward > 0) CVX.transfer(_msgSender(), _totalCvxReward);

224    if (_totalCrvReward > 0) CRV.transfer(_msgSender(), _totalCrvReward);

276    if (_cvxReward > 0) _rewards[1].amount = _cvxReward - _takenCVX;
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol

```solidity
File:   core/solidity/contracts/core/VaultController.sol

558       if (_fee > 0) usda.vaultControllerMint(owner(), _fee);

660       if (_tokenAmount > 0) _tokensToLiquidate = _tokenAmount;
```

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol

```solidity
File:    core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol

77       require(_mainValue > 0, 'invalid oracle value');

80       require(_anchorPrice > 0, 'invalid anchor value');
```

## [G-18] State variable can be packed into fewer `storage` slots

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas).
Reads of the variables are also cheaper.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol

```solidity
file: /contracts/core/VaultController.sol

61  uint96 public vaultsMinted;

63  uint256 public tokensRegistered;

65  uint192 public totalBaseLiability;

67  uint192 public protocolFee;

69  uint192 public initialBorrowingFee;

71  uint192 public liquidationFee;

```

### Recommended Code

```diff

    uint96 public vaultsMinted;

-   uint256 public tokensRegistered;

    uint192 public totalBaseLiability;

    uint192 public protocolFee;

    uint192 public initialBorrowingFee;

    uint192 public liquidationFee;

+   uint256 public tokensRegistered;
```

## [G-19] Use nested if and, avoid `multiple` check combinations

Using nested if is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L158

```solidity
file: /contracts/core/Vault.sol

158    if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L1018

```solidity
file: /contracts/core/VaultController.sol

1018    if (_increase && (_collateral.totalDeposited + _amount) > _collateral.cap) revert VaultController_CapReached();

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
file: /contracts/governance/GovernorCharlie.sol

170    if (amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender)) {


469    else if (
470      (_whitelisted && _proposal.againstVotes > _proposal.quorumVotes)
471        || (!_whitelisted && _proposal.forVotes <= _proposal.againstVotes)
472        || (!_whitelisted && _proposal.forVotes < _proposal.quorumVotes)

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol

```solidity
file: /contracts/utils/ThreeLines0_100.sol

34    if (!((0 < _r2) && (_r2 < _r1) && (_r1 < _r0))) revert ThreeLines0_100_InvalidCurve();

35    if (!((0 < _s1) && (_s1 < _s2) && (_s2 < 1e18))) revert ThreeLines0_100_InvalidBreakpointValues();

```

## [G-20] Non efficient zero initialization

Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
file: /contracts/governance/GovernorCharlie.sol

259    for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

313    for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

382    for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

```

## [G-21] Variable names that consist of all capital letters should be reserved for constant/immutable variables

If the variable needs to be different based on which class it comes from, a view/pure function should be used instead

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L70

```solidity
file: /contracts/utils/UFragments.sol

70  uint256 public MAX_SUPPLY; // = type(uint128).max; // (2^128) - 1

```
