|  No    | Issue   | Instance |
|------|---------|----------|  
|[G-01]|Can Make The Variable Outside The Loop To Save Gas |12|
|[G-02]|Expressions for constant values such as a call to keccak256(), should use immutable rather than constant|5|
|[G-03]|internal functions not called by the contract should be removed to save deployment gas|3|
|[G-04]|Use nested if statements instead of &&|7|
|[G-05]|Make 3 event parameters indexed when possible|31|
|[G-06]|Do not calculate constants|8|
|[G-07]|Use != 0 instead of > 0 for unsigned integer comparison|8|
|[G-08]|Use hardcode address instead address(this)|8|
|[G-09]|Using XOR (^) and OR (pipe line) bitwise equivalents|7|
|[G-10]|Shuold use arguments instead of state varible |16|
|[G-11]|keccak256() should only need to be called on a specific string literal once|4|
|[G-12]|Not using the named return variables when a function returns, wastes deployment gas|6|
|[G-13]|public functions to external|9|
|[G-14]|Amounts should be checked for 0 before calling a transfer|7|
|[G-15]|Duplicated require()/if() checks should be refactored to a modifier or function|5|
|[G-16]|Use assembly to write address storage values|10|


## [G-01] Can Make The Variable Outside The Loop To Save Gas 

creating a variable outside a loop can help save gas in Ethereum smart contracts if the variable is used multiple times inside the loop. This is because creating a new variable inside the loop for each iteration would incur additional gas costs due to the cost of initializing a new variable every time.

```solidity
177   uint256 _crvReward = _rewardsContract.earned(address(this));

187   uint256 _extraRewards = _rewardsContract.extraRewardsLength();

209   (uint256 _takenCVX, uint256 _takenCRV, uint256 _claimableAmph) =
          _amphClaimer.claimable(address(this), this.id(), _totalCvxReward, _totalCrvReward);

257   uint256 _earnedReward = _virtualReward.earned(address(this));          
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L177

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L187

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L257


```solidity
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
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L874



## [G-02] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

When it comes to gas optimization in Solidity, using immutable instead of constant for expressions with constant values such as a call to keccak256() can sometimes result in lower gas costs.

```solidity
21     bytes32 public constant VAULT_CONTROLLER_ROLE = keccak256('VAULT_CONTROLLER');
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L21

```solidity
19   bytes32 public constant DOMAIN_TYPEHASH =
    keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

23     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L19

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L23

```solidity
87   bytes32 public constant EIP712_DOMAIN =
    keccak256('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)');

89    bytes32 public constant PERMIT_TYPEHASH =
    keccak256('Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)');    
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L87

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L89



## [G-03] internal functions not called by the contract should be removed to save deployment gas


The reason for this is that every function in a contract adds to the size of the contract's bytecode, which in turn increases the gas cost of deploying and executing the contract. Therefore, removing unused internal functions can reduce the bytecode size and the gas cost of deploying and executing the contract


```solidity
12     function _getLpAddress(address _crvPool) internal view returns (address _lpAddress) {
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol#L12


```solidity
48   function _getVirtualPrice() internal view override returns (uint256 _value) {  
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol#L48

```solidity
19     function _setUnderlying(address _underlying) internal {
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L19


## [G-04]Use nested if statements instead of &&

In Solidity, using nested if statements instead of the logical AND operator (&&) can sometimes result in lower gas costs, especially when the conditions being checked have more complex expressions or function calls.

```solidity
88    if (_crvAmountToSend != 0 && _claimedAmph != 0) distributedAmph += (_claimedAmph / 1e12); // scale back to 1e6
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L88

```solidity
158     if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L158

```solidity
1018    if (_increase && (_collateral.totalDeposited + _amount) > _collateral.cap) revert VaultController_CapReached();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L1018



```solidity
170    if (amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender)) {

208    if (_emergency && !isWhitelisted(msg.sender)) {        
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L170

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L208


```solidity
34   if (!((0 < _r2) && (_r2 < _r1) && (_r1 < _r0))) revert ThreeLines0_100_InvalidCurve();

35   if (!((0 < _s1) && (_s1 < _s2) && (_s2 < 1e18))) revert ThreeLines0_100_InvalidBreakpointValues();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/ThreeLines0_100.sol#L34

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/ThreeLines0_100.sol#L35

## [G-05] Make 3 event parameters indexed when possible

One gas optimization technique for events is to make event parameters indexed when possible. In Solidity, event parameters can be marked as indexed, which allows for more efficient filtering of events by external actors.

```solidity
39  event LogRebase(uint256 indexed epoch, uint256 totalSupply);

40  event LogMonetaryPolicyUpdated(address monetaryPolicy);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L39

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L40

```solidity
20    event ClaimedAmph(
    address indexed _vaultClaimer, uint256 _cvxTotalRewards, uint256 _crvTotalRewards, uint256 _amphAmount
  );

36   event RecoveredDust(address indexed _token, address _receiver, uint256 _amount);

42    event ChangedCvxRewardFee(uint256 _newCvxReward);

48     event ChangedCrvRewardFee(uint256 _newCrvReward);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IAMPHClaimer.sol#L20

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IAMPHClaimer.sol#L36

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IAMPHClaimer.sol#L42

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IAMPHClaimer.sol#L48



```solidity
19    event Deposit(address indexed _from, uint256 _value);

26      event Withdraw(address indexed _from, uint256 _value);

33    event Mint(address _to, uint256 _value);

40     event Burn(address _from, uint256 _value);

48      event Donation(address indexed _from, uint256 _value, uint256 _totalSupply);

55      event RecoveredDust(address indexed _receiver, uint256 _amount);

68   event VaultControllerTransfer(address _target, uint256 _susdAmount);

```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IUSDA.sol#L19

```solidity
20     event Deposit(address _token, uint256 _amount);

27       event Withdraw(address _token, uint256 _amount);

34     event ClaimedReward(address _token, uint256 _amount);

41      event Staked(address _token, uint256 _amount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IVault.sol#L20

```solidity
23    event InterestEvent(uint64 _epoch, uint192 _amount, uint256 _curveVal);

29     event NewProtocolFee(uint192 _protocolFee);

39     event RegisteredErc20(
    address _tokenAddress, uint256 _ltv, address _oracleAddress, uint256 _liquidationIncentive, uint256 _cap
  );


52    event UpdateRegisteredErc20(
    address _tokenAddress,
    uint256 _ltv,
    address _oracleAddress,
    uint256 _liquidationIncentive,
    uint256 _cap,
    uint256 _poolId
  );


67    event NewVault(address _vaultAddress, uint256 _vaultId, address _vaultOwner);

73     event RegisterCurveMaster(address _curveMasterAddress);

81     event BorrowUSDA(uint256 _vaultId, address _vaultAddress, uint256 _borrowAmount, uint256 _fee);

89     event RepayUSDA(uint256 _vaultId, address _vaultAddress, uint256 _repayAmount);


99     event Liquidate(
    uint256 _vaultId,
    address _assetAddress,
    uint256 _usdaToRepurchase,
    uint256 _tokensToLiquidate,
    uint256 _liquidationFee
  );

118    event RegisterUSDA(address _usdaContractAddress);

125     event ChangedInitialBorrowingFee(uint192 _oldBorrowingFee, uint192 _newBorrowingFee);


132     event ChangedLiquidationFee(uint192 _oldLiquidationFee, uint192 _newLiquidationFee);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IVaultController.sol#L23

```solidity
16     event VaultControllerSet(address _oldVaultControllerAddress, address _newVaultControllerAddress);

24      event CurveSet(address _oldCurveAddress, address _token, address _newCurveAddress);

32      event CurveForceSet(address _oldCurveAddress, address _token, address _newCurveAddress);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/periphery/ICurveMaster.sol#L16

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/periphery/ICurveMaster.sol#L24


https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/periphery/ICurveMaster.sol#L32

## [G-06] Do not calculate constants

In Solidity, constants are values that are known at compile-time and cannot be changed at runtime. When writing contracts, it's important to optimize gas usage to reduce the cost of executing the contract and to prevent running out of gas, which can cause the transaction to fail.

```solidity
18     uint256 internal constant _FIFTY_MILLIONS = 50_000_000 * 1e6;

21     uint256 internal constant _TWENTY_FIVE_THOUSANDS = 25_000 * 1e6;

24     uint256 internal constant _FIFTY = 50 * 1e6;

27     uint256 public constant BASE_SUPPLY_PER_CLIFF = 8_000_000 * 1e6;

30     uint256 public constant TOTAL_CLIFFS = 1000;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L18

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L21


https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L24


https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L27


https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L30


```solidity
35     uint256 public constant MAX_wUSDA_SUPPLY = 10_000_000 * (10 ** 18); // 10 M
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L35

```solidity
62   uint256 private constant MAX_UINT256 = 2 ** 256 - 1;

63   uint256 private constant INITIAL_FRAGMENTS_SUPPLY = 1 * 10 ** DECIMALS;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L62

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L63



## [G-07] Use != 0 instead of > 0 for unsigned integer comparison

The reason for this is that the != 0 operator is a simpler and cheaper operation than the > 0 operator. The != 0 operator simply checks if the value is non-zero, while the > 0 operator checks if the value is greater than zero, which requires an additional comparison operation.

```solidity
206    if (_totalCrvReward > 0 || _totalCvxReward > 0) {

223    if (_totalCvxReward > 0) CVX.transfer(_msgSender(), _totalCvxReward);

224    if (_totalCrvReward > 0) CRV.transfer(_msgSender(), _totalCrvReward);

276    if (_cvxReward > 0) _rewards[1].amount = _cvxReward - _takenCVX;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L206

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L223

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L224

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L276

```solidity
558       if (_fee > 0) usda.vaultControllerMint(owner(), _fee);

660       if (_tokenAmount > 0) _tokensToLiquidate = _tokenAmount;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L558

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L660

```solidity
77       require(_mainValue > 0, 'invalid oracle value');

80       require(_anchorPrice > 0, 'invalid anchor value');
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L77

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L80


## [G-08] Use hardcode address instead address(this)


The statement "Use hardcoded address instead of address(this) to save gas cost" means that using a hardcoded address instead of the address(this) expression can be more gas-efficient when sending or receiving Ether to or from the contract. This is because using the address(this) expression requires additional gas to be consumed to retrieve the address of the current contract, while using a hardcoded address does not.


```solidity
92       SafeERC20Upgradeable.safeTransferFrom(IERC20Upgradeable(_token), _msgSender(), address(this), _amount);

177      uint256 _crvReward = _rewardsContract.earned(address(this));

182     _rewardsContract.getReward(address(this), false);

191      uint256 _earnedReward = _virtualReward.earned(address(this));

210     _amphClaimer.claimable(address(this), this.id(), _totalCvxReward, _totalCrvReward);

245      uint256 _crvReward = _rewardsContract.earned(address(this));

257      uint256 _earnedReward = _virtualReward.earned(address(this));

271      (_takenCVX, _takenCRV, _claimableAmph) = _amphClaimer.claimable(address(this), this.id(), _cvxReward, _crvReward);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L92


https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L177

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L182

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L191

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L210

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L245

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L275

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L271

## [G-09] Using XOR (^) and OR (pipe line) bitwise equivalents

The reason for this is that bitwise operations are simpler and cheaper operations than logical operators. Bitwise operations work on individual bits of binary values, while logical operators work on entire boolean values.

```solidity

169      if (_crvTotalRewards == 0 || _amphBalance == 0) return (0, 0, 0);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L169

```solidity
206   if (_totalCrvReward > 0 || _totalCvxReward > 0) {    
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L206


```solidity
174     _targets.length != _values.length || _targets.length != _signatures.length || _targets.length != _calldatas.length

372      || msg.sender != whitelistGuardian

463     if (proposalCount < _proposalId || _proposalId <= initialProposalId) revert GovernorCharlie_InvalidProposalId();

471    || (!_whitelisted && _proposal.forVotes <= _proposal.againstVotes)
        || (!_whitelisted && _proposal.forVotes < _proposal.quorumVotes)        
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L174

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L372

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L463

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L471


```solidity
41       _stale = AGGREGATOR.isStale() || BASE_AGGREGATOR.isStale();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L41

```solidity
57       if (_to == address(0) || _to == address(this)) revert UFragments_InvalidRecipient();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L57

## [G-10] Shuold use arguments instead of state varible

In Solidity, using function arguments instead of state variables can sometimes result in lower gas costs, especially for functions that are called frequently or in loops.


```solidity
105       emit Transfer(address(this), address(0x0), _totalSupply);

286       emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);

301       emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L105

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L286

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L301



```solidity
150       emit Staked(_tokenAddress, balances[_tokenAddress]);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L150

```solidity
290      emit NewVault(_vaultAddress, vaultsMinted, _msgSender());
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L290

```solidity
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
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L587

## [G‑11] keccak256() should only need to be called on a specific string literal once

In Solidity, keccak256() is a hashing function that is commonly used for generating unique identifiers or for verifying data integrity. When using keccak256() with a specific string literal, it's generally more gas-efficient to call the function only once and store the result in a variable for future use.


```solidity
19     bytes32 public constant DOMAIN_TYPEHASH =
    keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

23      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L19

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L23



```solidity
87    bytes32 public constant EIP712_DOMAIN =
    keccak256('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)');

89     bytes32 public constant PERMIT_TYPEHASH =
    keccak256('Permit(address owner,address spender,uint256 value,uint256 nnoce,uint256 deadline)');    
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L87

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L89

## [G-12] Not using the named return variables when a function returns, wastes deployment gas

When named return variables are used, Solidity generates additional code to initialize the return variables with their default values. This initialization code is only generated when named return variables are present, and is not generated when unnamed return variables are used.

```solidity
121     return _totalSupply;

129     return _gonBalances[_who] / _gonsPerFragment;

137     return _gonBalances[_who];

145     return _totalGons;

154     return _nonces[_who];

168      return keccak256(
      abi.encode(EIP712_DOMAIN, keccak256(bytes(name)), keccak256(bytes(EIP712_REVISION)), _chainId, address(this))
    );

```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L121

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L129

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L137

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L145

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L154

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L168




## [G-13] public functions to external

Using external functions instead of public functions can sometimes result in lower gas costs, especially for functions that are called frequently or in loops.


```solidity
278    function mintVault() public override whenNotPaused returns (address _vaultAddress) {    

988   function vaultSummaries(
    uint96 _start,
    uint96 _stop
  ) public view override returns (VaultSummary[] memory _vaultSummaries) {
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L278

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L988


```solidity
120    function propose(
    address[] memory _targets,
    uint256[] memory _values,
    string[] memory _signatures,
    bytes[] memory _calldatas,
    string memory _description
  ) public override returns (uint256 _proposalId) {

139    function proposeEmergency(
    address[] memory _targets,
    uint256[] memory _values,
    string[] memory _signatures,
    bytes[] memory _calldatas,
    string memory _description
  ) public override returns (uint256 _proposalId) {

583    function setDelay(uint256 _proposalTimelockDelay) public override onlyGov {

594    function setEmergencyDelay(uint256 _emergencyTimelockDelay) public override onlyGov {
            
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L120

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L139

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L583

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L594



```solidity
153     function nonces(address _who) public view returns (uint256 _addressNonces) {

283      function increaseAllowance(address _spender, uint256 _addedValue) public returns (bool _success) {


315    function permit(
    address _owner,
    address _spender,
    uint256 _value,
    uint256 _deadline,
    uint8 _v,
    bytes32 _r,
    bytes32 _s
  ) public {
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L153

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L283

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L315


## [G-14] Amounts should be checked for 0 before calling a transfer


The reason for this is that sending 0 as the amount in a transfer or send function call can result in the transaction being reverted, which incurs gas costs. By checking the amount for 0 before making the call, we can avoid the unnecessary gas costs associated with a reverted transaction.

```solidity
129       IERC20(_token).transfer(owner(), _amount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L29

```solidity
216    sUSD.transfer(_to, _amount);

246    sUSD.transfer(_target, _susdAmount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L216

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L246

```solidity
129       SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_tokenAddress), _msgSender(), _amount);

285         SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_token), _to, _amount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#129

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#285


```solidity
215     IERC20(USDA).safeTransferFrom(_from, address(this), _usdaAmount);

228     IERC20(USDA).safeTransfer(_to, _usdaAmount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L215

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L228


## [G-15] Duplicated require()/if() checks should be refactored to a modifier or function

By using a modifier or a function to perform the checks, we can reduce the amount of duplicate code and potentially reduce gas costs. Modifiers and functions can also make the code more readable and easier to maintain.

```solidity
File:    core/solidity/contracts/core/USDA.sol
102       if (_susdAmount == 0) revert USDA_ZeroAmount();

149       if (_susdAmount == 0) revert USDA_ZeroAmount();

162       if (_susdAmount == 0) revert USDA_ZeroAmount();

183       if (_susdAmount == 0) revert USDA_ZeroAmount();

203       if (_susdAmount == 0) revert USDA_ZeroAmount();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L102

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L149

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L162

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L183

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L203



## [G-16] Use assembly to write address storage values

One way to reduce gas costs when working with address storage values is to use assembly code to directly read and write the values in storage. Assembly code can be more gas-efficient than Solidity code because it allows for fine-grained control over the memory layout and data operations.

```solidity
61    vaultController = IVaultController(_vaultController);

120   vaultController = IVaultController(_newVaultController);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L61

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L120

```solidity
64   pauser = _pauser;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L64

```solidity
49   USDA = _usdaToken;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L49

```solidity
44    anchorRelay = IOracleRelay(_anchorAddress);

46     mainRelay = IOracleRelay(_mainAddress);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L44

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L46


```solidity
40   _AGGREGATOR = AggregatorV2V3Interface(_feedAddress);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L40

```solidity
21   cToken = ICToken(_cToken);

58    anchoredViewUnderlying = IOracleRelay(_anchoredViewUnderlying);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L21

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L58


```solidity
42    LOOKBACK = _lookback;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L42

