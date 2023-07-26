## [L-01] Calls to `VaultController::updateRegisteredErc20()` could render vaults instantly insolvent

updateRegisteredErc20() has the ability to modify the LTV ratio of a token across the protocol to any arbitrary, potentially dangerously low value.
If this value is lowered so that the allowed loan amount for a given deposit value drops, then, in a single transaction, any vault that has borrowed an amount that is within the old LTV but above the new LTV would become instantly insolvent.
This would then cause them to be liquidated, damaging the protocol.
This is mitigated by the presumed procedure of such a change, which would work through governance and so there would be time for vault owners to react during the proposal and the timelock implementation period.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L383-L412

```solidity
File: contracts/core/VaultController.sol

394    if (_ltv >= (EXP_SCALE - _liquidationIncentive)) revert VaultController_LTVIncompatible();
395    if (_poolId != 0) {
396      (address _lpToken,,, address _crvRewards,,) = BOOSTER.poolInfo(_poolId);
397     if (_lpToken != _tokenAddress) revert VaultController_TokenAddressDoesNotMatchLpAddress();
398      _collateral.collateralType = CollateralType.CurveLPStakedOnConvex;
399      _collateral.crvRewardsContract = IBaseRewardPool(_crvRewards);
400      _collateral.poolId = _poolId;
401    }
402    // set the oracle of the token
403    _collateral.oracle = IOracleRelay(_oracleAddress);
404    // set the ltv of the token
405    _collateral.ltv = _ltv;
```

## [L-02] Whitelisting can expire mid way through a proposal

Whitelisted proposals can expire during the assessment period, making a user, and a proposal, lose its status and beneﬁts.

Diﬀerent procedures and privileges are enacted for proposers based on whether they are whitelisted or not.

The assessment of whether an account is whitelisted is based on the test performed in `isWhitelisted()`,where `whitelistAccountExpirations[_account]` is compared to the current time.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L559-L561

```solidity
File:  contracts/governance/GovernorCharlie.sol

559  function isWhitelisted(address _account) public view override returns (bool _isWhitelisted) {
560    return (whitelistAccountExpirations[_account] > block.timestamp);
561  }
```

Consider a situation in which a whitelisted proposer makes a proposal and then their whitelisting expires during the assessment period. The entire proposal would then lose its whitelisted status, notably allowing cancellation should the proposer drop below proposal threshold.

### Recommendations

When a proposal is created by a whitelisted proposer, consider marking it as whitelisted, and then assess the proposals status according to this value, rather than continuously assessing the status of the account that proposed it.

## [L-03] Inaccurate `GovernorCharlie.sol::votingPeriod` and `GovernorCharlie.sol::votingDelay` time measure

As `votingPeriod` and `votingDelay` times are measured in blocks, they may produce inconsistent results.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L90-L91

```solidity
File:  contracts/governance/GovernorCharlie.sol

90:    votingPeriod = 40_320;
91:    votingDelay = 13_140;

//(...)


187     Proposal memory _newProposal = Proposal({
188       id: proposalCount,
189       proposer: msg.sender,
190       eta: 0,
191       targets: _targets,
192       values: _values,
193       signatures: _signatures,
194       calldatas: _calldatas,
195:      startBlock: block.number + votingDelay,// @audit block.number is inaccurate
196:      endBlock: block.number + votingDelay + votingPeriod,


//(...)
```

With Ethereum’s move to proof-of-stake (PoS) after The Merge, block.number is now considered an inaccurate method of measuring time as the block times are no longer consistent.

### Recommendations

Use `block.timestamp` for time measurement to ensure best accuracy and consistency.

## [L-04] Malicious modiﬁcation of `VaultController::registerUSDA` or `VaultController::registerCurveMaster` would allow the removal of all user funds

VaultController has two functions registerUSDA() and registerCurveMaster() which allow modiﬁcation of the
key variables that the contract uses. Either of these functions, if ever used mistakenly or maliciously, could have widespread eﬀects on all funds held in the protocol.

If `usda` were set to a malicious value, an attacker could zero all balances except their own, mint themselves endless tokens, and then liquidate all vaults and empty the reserve.

If `curveMaster` were set to a malicious value, an attacker could cause all vaults to become insolvent by delivering false asset prices and then liquidate all the vaults.

Note, these threats are heavily mitigated by the fact that the functions are modiﬁed with onlyOwner , so the chances of them being called by an external, malicious actor are very low.

Nevertheless, if these functions are only needed in setup, their continued existence creates an unnecessarily powerful tool for accessing all the protocol’s funds in the event that an attack on governance did succeed.

Consider whether registerUSDA() and registerOracleMaster() provide useful ongoing utility. If not, remove them and make usda or curveMaster immutable variables deﬁned in the constructor.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L312-L323

```solidity
File: contracts/core/VaultController.sol

312  function registerCurveMaster(address _masterCurveAddress) external override onlyOwner {
313    curveMaster = CurveMaster(_masterCurveAddress);
314    emit RegisterCurveMaster(_masterCurveAddress);
315  }




319  function changeProtocolFee(uint192 _newProtocolFee) external override onlyOwner {
320    if (_newProtocolFee >= 1e18) revert VaultController_FeeTooLarge();
321    protocolFee = _newProtocolFee;
322    emit NewProtocolFee(_newProtocolFee);
323  }
```

## [L-05] Un-indexed Parameters in Events

Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute indexed which will cause the respective arguments to be treated as log topics instead of data. Here is one of the instances entailed:

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICurveMaster.sol

```solidity
File:  core/solidity/interfaces/periphery/ICurveMaster.sol


16  event VaultControllerSet(address _oldVaultControllerAddress, address _newVaultControllerAddress);

24  event CurveSet(address _oldCurveAddress, address _token, address _newCurveAddress);

32  event CurveForceSet(address _oldCurveAddress, address _token, address _newCurveAddress);

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol

```solidity
File: solidity/interfaces/core/IVaultController.sol


23  event InterestEvent(uint64 _epoch, uint192 _amount, uint256 _curveVal);

29  event NewProtocolFee(uint192 _protocolFee);

39  event RegisteredErc20(
40    address _tokenAddress, uint256 _ltv, address _oracleAddress, uint256 _liquidationIncentive, uint256 _cap
41  );


52  event UpdateRegisteredErc20(
53    address _tokenAddress,
54    uint256 _ltv,
55    address _oracleAddress,
56    uint256 _liquidationIncentive,
57    uint256 _cap,
58    uint256 _poolId
59  );

67  event NewVault(address _vaultAddress, uint256 _vaultId, address _vaultOwner);

73  event RegisterCurveMaster(address _curveMasterAddress);

81  event BorrowUSDA(uint256 _vaultId, address _vaultAddress, uint256 _borrowAmount, uint256 _fee);

89  event RepayUSDA(uint256 _vaultId, address _vaultAddress, uint256 _repayAmount);

99  event Liquidate(
100    uint256 _vaultId,
101    address _assetAddress,
102    uint256 _usdaToRepurchase,
103    uint256 _tokensToLiquidate,
104    uint256 _liquidationFee
105  );

112  event ChangedClaimerContract(IAMPHClaimer _oldClaimerContract, IAMPHClaimer _newClaimerContract);

118  event RegisterUSDA(address _usdaContractAddress);

125  event ChangedInitialBorrowingFee(uint192 _oldBorrowingFee, uint192 _newBorrowingFee);

132  event ChangedLiquidationFee(uint192 _oldLiquidationFee, uint192 _newLiquidationFee);

139  event CollateralsMigratedFrom(IVaultController _oldVaultController, address[] _tokenAddresses);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVault.sol

```solidity
File: core/solidity/interfaces/core/IVault.sol


20  event Deposit(address _token, uint256 _amount);

27  event Withdraw(address _token, uint256 _amount);

34  event ClaimedReward(address _token, uint256 _amount);

41  event Staked(address _token, uint256 _amount);

```

## [L-06] Pragma Experimental ABIEncoderV2 is Deprecated

As of Solidity version 0.8.0, the pragma experimental ABIEncoderV2; directive has been deprecated, and it's no longer necessary to enable experimental ABIEncoderV2 explicitly. Instead, the new ABIEncoderV2 is now enabled by default in Solidity versions 0.8.0 and above.

So, if you are using Solidity version 0.8.0 or later, you can safely remove the pragma experimental ABIEncoderV2; directive from your contract, and the new ABIEncoderV2 functionality will be available automatically.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L4

```solidity
File: contracts/governance/GovernorCharlie.sol

4   pragma experimental ABIEncoderV2;

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IAmphoraProtocolToken.sol#L3

```solidity
File: solidity/interfaces/governance/IAmphoraProtocolToken.sol

3   pragma experimental ABIEncoderV2;

```

### Recommendation

Use pragma abicoder v2 instead.

## [L-07] Do not perform arithmetic operation inside the `emit` of an event

It is not recommended to perform arithmetic operations inside the emit of an event. The emits are placed to log the function results for the use of the off-chain tools and not to perform arithmetic operations. The arithmetic opeations should be performed outside the emit and only the result should be logged and emitted in an event for the use of off-chain tools.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L694

```solidity
File: contracts/core/VaultController.sol

694    emit Liquidate(_id, _assetAddress, _usdaToRepurchase, _tokensToLiquidate - _liquidationFee, _liquidationFee);

```

## [L-08] `_safeMint()` should be used rather than `_mint()` wherever possible

`_mint()` is discouraged in favor of `_safeMint()` which ensures that the recipient is either an EOA,Both OpenZeppelin and solmate have versions of this function

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L216

```solidity
File: contracts/core/WUSDA.sol

216    _mint(_to, _wusdaAmount);

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol

```solidity
File: contracts/governance/AmphoraProtocolToken.sol

16    _mint(_account, _initialSupply);

21    _mint(_dst, _rawAmount);

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L104

```solidity
File: contracts/core/USDA.sol

104    _mint(_target, _susdAmount);

163    _mint(_msgSender(), _susdAmount);

225    _mint(_target, _amount);

```
