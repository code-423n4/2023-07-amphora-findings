## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
// place this modifier before the constructor
106:  modifier onlyGov() {
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol

```solidity
// place this internal function after all the others
19:  function _setUnderlying(address _underlying) internal {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol

```solidity
// place this external function right after the constructor
40:  function isStale() external view returns (bool _stale) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol

```solidity
// place this right after the constructor
54:  fallback() external {}
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol

```solidity
// place this external function before all the other ones
57:  function changeAnchoredView(address _anchoredViewUnderlying) external onlyOwner {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol

```solidity
// place this public function after the external ones.
51:  function peekValue() public view override returns (uint256 _value) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol

```solidity
// public function coming before external one, should come after.
40:  function peekValue() public view override returns (uint256 _value) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol

```solidity
// external functions coming after public ones, should come before
179:  function transfer(address _to, uint256 _value) external override validRecipient(_to) returns (bool _success) {
194:  function transferAll(address _to) external validRecipient(_to) returns (bool _success) {
211:  function allowance(address _owner, address _spender) external view override returns (uint256 _remaining) {
222:  function transferFrom(
243:  function transferAllFrom(address _from, address _to) external validRecipient(_to) returns (bool _success) {
268:  function approve(address _spender, uint256 _value) external override returns (bool _success) {
297:  function decreaseAllowance(address _spender, uint256 _subtractedValue) external returns (bool _success) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol

```solidity
// internal functions coming before external ones
101:  function _deposit(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
147:  function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
167:  function _mint(address _target, uint256 _amount) internal {
188:  function _burn(address _target, uint256 _amount) internal {
259:  function _donation(uint256 _amount) internal {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
// public functions coming before external ones
120:  function propose(
139:  function proposeEmergency(
462:  function state(uint256 _proposalId) public view override returns (ProposalState _state) {
559:  function isWhitelisted(address _account) public view override returns (bool _isWhitelisted) {
583:  function setDelay(uint256 _proposalTimelockDelay) public override onlyGov {
594:  function setEmergencyDelay(uint256 _emergencyTimelockDelay) public override onlyGov {

// internal functions coming before some external ones
159:  function _propose(
289:  function _queueTransaction(
398:  function _cancelTransaction(
531:  function _castVoteInternal(
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol

```solidity
// internal function coming before public and external ones
252:  function _migrateCollateralsFrom(IVaultController _oldVaultController, address[] memory _tokenAddresses) internal {
527:  function _borrow(uint96 _id, uint192 _amount, address _target, bool _isUSDA) internal paysInterest whenNotPaused {
584:  function _repay(uint96 _id, uint192 _amountInUSDA, bool _repayAll) internal paysInterest whenNotPaused {
721:  function _peekLiquidationMath(
743:  function _liquidationMath(
768:  function _calculateTokensToLiquidate(
806:  function _getVault(uint96 _id) internal view returns (IVault _vault) {
824:  function _peekAmountToSolvency(uint96 _id) internal view returns (uint256 _usdaToSolvency) {
831:  function _amountToSolvency(uint96 _id) internal returns (uint256 _usdaToSolvency) {
846:  function _vaultLiability(uint96 _id) internal view returns (uint192 _liability) {
979:  function _createVault(uint96 _id, address _minter) internal virtual returns (address _vault) {
1015:  function _modifyTotalDeposited(uint256 _amount, address _token, bool _increase) internal {
1039:  function _getPriceWithDecimals(uint256 _price, uint256 _decimals) internal pure returns (uint256 _priceWithDecimals) {

// private functions should come last
866:  function _getVaultBorrowingPower(IVault _vault) private returns (uint192 _borrowPower) {
894:  function _peekVaultBorrowingPower(IVault _vault) private view returns (uint192 _borrowPower) {
928:  function _payInterest() private returns (uint256 _interest) {

// public function coming before some external ones
278:  function mintVault() public override whenNotPaused returns (address _vaultAddress) {
447:  function peekCheckVault(uint96 _id) public view override returns (bool _overCollateralized) {
462:  function checkVault(uint96 _id) public returns (bool _overCollateralized) {
499:  function getBorrowingFee(uint192 _amount) public view override returns (uint192 _fee) {
508:  function getLiquidationFee(
988:  function vaultSummaries(
```

---

### natSpec missing [3]

Some functions are missing @params or @returns. Specification Format.” These are written with a triple slash (///) or a double asterisk block(/** ... */) directly above function declarations or statements to generate documentation in JSON format for developers and end-users. It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). These comments contain different types of tags:
- @title: A title that should describe the contract/interface @author: The name of the author (for contract, interface) 
- @notice: Explain to an end user what this does (for contract, interface, function, public state variable, event) 
- @dev: Explain to a developer any extra details (for contract, interface, function, state variable, event) 
- @param: Documents a parameter (just like in doxygen) and must be followed by parameter name (for function, event)
- @return: Documents the return variables of a contract’s function (function, public state variable)
- @inheritdoc: Copies all missing tags from the base function and must be followed by the contract name (for function, public state variable)
- @custom…: Custom tag, semantics is application-defined (for everywhere)

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurveSlave.sol

```solidity
7:  function valueAt(int256 _xValue) external view returns (int256 _value);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/IChainlinkOracleRelay.sol

```solidity
4:  interface IChainlinkOracleRelay {

// @return missing
6:    function isStale() external view returns (bool _stale);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IAMPH.sol

```solidity
4:  interface IAMPH {

// @params missing
12:  function mint(address _dst, uint256 _rawAmount) external;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICToken.sol

```solidity
4:  interface ICToken {

// @return imssing
6:  function exchangeRateStored() external view returns (uint256 _exchangeRate);
9:  function decimals() external view returns (uint8 _decimals);
12:  function underlying() external view returns (address _underlying);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICVX.sol

```solidity
9:  interface ICVX is IERC20 {
10:  function totalCliffs() external view returns (uint256 _totalCliffs);
11:  function reductionPerCliff() external view returns (uint256 _reduction);
12:  function maxSupply() external view returns (uint256 _maxSupply);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICurveAddressesProvider.sol

```solidity
// @return missgin
8:  function get_registry() external view returns (address _registry);

// @param and @return missing
15:  function get_lp_token(address _pool) external view returns (address _lpToken);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IAmphoraProtocolToken.sol

```solidity
21:  function mint(address _dst, uint256 _rawAmount) external;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IVirtualBalanceRewardPool.sol

```solidity
6:  interface IVirtualBalanceRewardPool {
7:  function rewardToken() external view returns (IERC20 _rewardToken);
8:  function earned(address _ad) external view returns (uint256 _reward);
9:  function getReward() external;
10:  function queueNewRewards(uint256 _rewards) external;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IBooster.sol

```solidity
4:  interface IBooster {
5:  function owner() external view returns (address _owner);
6:  function setVoteDelegate(address _voteDelegate) external;
7:  function vote(uint256 _voteId, address _votingAddress, bool _support) external returns (bool _success);
8:  function voteGaugeWeight(address[] calldata _gauge, uint256[] calldata _weight) external returns (bool _success);
9:  function poolInfo(uint256 _pid)
13:  function earmarkRewards(uint256 _pid) external returns (bool _claimed);
14:  function earmarkFees() external returns (bool _claimed);
15:  function deposit(uint256 _pid, uint256 _amount, bool _stake) external returns (bool _success);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICurveMaster.sol

```solidity
// @return missing
49:  function vaultControllerAddress() external view returns (address _vaultController);
58:  function curves(address _tokenAddress) external view returns (address _curve);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IBaseRewardPool.sol

```solidity
7:  interface IBaseRewardPool {
8:  function stake(uint256 _amount) external returns (bool _staked);
9:  function stakeFor(address _for, uint256 _amount) external returns (bool _staked);
10:  function withdraw(uint256 _amount, bool _claim) external returns (bool _success);
11:  function withdrawAndUnwrap(uint256 _amount, bool _claim) external returns (bool _success);
12:  function getReward(address _account, bool _claimExtras) external returns (bool _success);
13:  function rewardToken() external view returns (IERC20 _rewardToken);
14:  function earned(address _ad) external view returns (uint256 _reward);
15:  function extraRewardsLength() external view returns (uint256 _extraRewardsLength);
16:  function extraRewards(uint256 _position) external view returns (IVirtualBalanceRewardPool _virtualReward);
17:  function queueNewRewards(uint256 _rewards) external returns (bool _success);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IWUSDA.sol

```solidity
// @return missing
34:  function USDA() external view returns (address _usda);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol

```solidity
6:  interface ICurvePool {
7:  function get_virtual_price() external view returns (uint256 _price);
8:  function gamma() external view returns (uint256 _gamma);
10:  function A() external view returns (uint256 _A);
11:  function calc_token_amount(uint256[] memory _amounts, bool _deposit) external view returns (uint256 _amount);
14:  interface IStablePool is ICurvePool {
15:  function lp_token() external view returns (IERC20 _lpToken);
16:  function remove_liquidity(
20:  function add_liquidity(
26:  interface IV2Pool is ICurvePool {
27:  function token() external view returns (IERC20 _lpToken);
28:  function claim_admin_fees() external;
29:  function add_liquidity(
34:  function remove_liquidity(uint256 _amount, uint256[2] memory _minAmounts, bool _useEth) external;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IAMPHClaimer.sol

```solidity
55:  function CVX() external view returns (IERC20 _cvx);
58:  function CRV() external view returns (IERC20 _crv);
61:  function AMPH() external view returns (IERC20 _amph);
64:  function BASE_SUPPLY_PER_CLIFF() external view returns (uint256 _baseSupplyPerCliff);
67:  function distributedAmph() external view returns (uint256 _distributedAmph);
70:  function TOTAL_CLIFFS() external view returns (uint256 _totalCliffs);
73:  function cvxRewardFee() external view returns (uint256 _cvxRewardFee);
76:  function crvRewardFee() external view returns (uint256 _crvRewardFee);
79:  function vaultController() external view returns (IVaultController _vaultController);

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol

```solidity
// @return missing
124:  function pauser() external view returns (address _pauser);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IGovernorCharlie.sol

```solidity
9:  interface IGovernorCharlie is IGovernorCharlieEvents {
138:  function optimisticVotingDelay() external view returns (uint256 _optimisticVotingDelay);

// @return missing
91:  function quorumVotes() external view returns (uint256 _quorumVotes);
94:  function emergencyQuorumVotes() external view returns (uint256 _emergencyQuorumVotes);
97:  function votingDelay() external view returns (uint256 _votingDelay);
100:  function votingPeriod() external view returns (uint256 _votingPeriod);
103:  function proposalThreshold() external view returns (uint256 _proposalThreshold);
106:  function initialProposalId() external view returns (uint256 _initialProposalId);
109:  function proposalCount() external view returns (uint256 _proposalCount);
112:  function amph() external view returns (IAMPH _amph);
115:  function latestProposalIds(address _proposer) external returns (uint256 _proposerId);
118:  function queuedTransactions(bytes32 _transaction) external returns (bool _isQueued);
121:  function proposalTimelockDelay() external view returns (uint256 _proposalTimelockDelay);
124:  function whitelistAccountExpirations(address _account) external returns (uint256 _expiration);
127:  function whitelistGuardian() external view returns (address _guardian);
130:  function emergencyVotingPeriod() external view returns (uint256 _emergencyVotingPeriod);
133:  function emergencyTimelockDelay() external view returns (uint256 _emergencyTimelockDelay);
136:  function optimisticQuorumVotes() external view returns (uint256 _optimisticQuorumVotes);

// delete @param since the function does not take any,  @return missing
145:  function timelock() external view returns (address _timelock);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol

```solidity
212:  struct VaultSummary {
220:  struct Interest {
225:  struct CollateralInfo {
365:  function tokenAddressCollateralInfo(address _token)

// @return missing
243:  function tokensRegistered() external view returns (uint256 _tokensRegistered);
246:  function vaultsMinted() external view returns (uint96 _vaultsMinted);
253:  function totalBaseLiability() external view returns (uint192 _totalBaseLiability);
260:  function protocolFee() external view returns (uint192 _protocolFee);
263:  function MAX_INIT_BORROWING_FEE() external view returns (uint192 _maxInitBorrowingFee);
266:  function initialBorrowingFee() external view returns (uint192 _initialBorrowingFee);
269:  function liquidationFee() external view returns (uint192 _liquidationFee);
281:  function curveMaster() external view returns (CurveMaster _curveMaster);
335:  function BOOSTER() external view returns (IBooster _booster);
338:  function claimerContract() external view returns (IAMPHClaimer _claimerContract);
341:  function VAULT_DEPLOYER() external view returns (IVaultDeployer _vaultDeployer);
344:  function MAX_DECIMALS() external view returns (uint8 _maxDecimals);
382:  function interest() external view returns (uint64 _lastTime, uint192 _factor);
385:  function usda() external view returns (IUSDA _usda);

// @param missing
278:  function enabledTokens(uint256 _index) external view returns (address _enabledToken);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol

```solidity
6:  abstract contract CurveRegistryUtils {

// @param and @return missing
12:  function _getLpAddress(address _crvPool) internal view returns (address _lpAddress) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol

```solidity
6:  library ChainlinkStalePriceLib {

// @param and @return missing
11:  function getCurrentPrice(AggregatorV2V3Interface _aggregator) internal view returns (uint256 _price) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol

```solidity
6:  abstract contract OracleRelay is IOracleRelay {
14:  constructor(OracleType _oracleType) {

// @param missing
19:  function _setUnderlying(address _underlying) internal {

// @return missing
24:  function currentValue() external virtual returns (uint256 _currentValue) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol

```solidity
8:  contract AmphoraProtocolToken is IAmphoraProtocolToken, ERC20VotesComp, Ownable {
9:  constructor(

// @params missing
20:  function mint(address _dst, uint256 _rawAmount) public onlyOwner {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/WstEthOracle.sol

```solidity
8:  contract WstEthOracle is OracleRelay {
14:  constructor(IOracleRelay _stEthAnchoredViewUnderlying) OracleRelay(OracleType.Chainlink) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol

```solidity
8:  contract EthSafeStableCurveOracle is StableCurveLpOracle {
11:  constructor(

// notice or dev missing
48:  function _getVirtualPrice() internal view override returns (uint256 _value) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol

```solidity
10:  contract CTokenOracle is OracleRelay, Ownable {
20:  constructor(address _cToken, IOracleRelay _anchoredViewUnderlying) OracleRelay(OracleType.Chainlink) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/TriCrypto2Oracle.sol

```solidity
26:  constructor(
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol

```solidity
19:  constructor(address _crvPool, IOracleRelay[] memory _anchoredUnderlyingTokens) OracleRelay(OracleType.Chainlink) {

// @return missing
41:  function _get() internal view returns (uint256 _value) {
57:  function _getVirtualPrice() internal view virtual returns (uint256 _value) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol

```solidity
24:  constructor(

// @dev or @notice missing
46:  function currentValue() external override returns (uint256 _currentValue) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol

```solidity
79:  function _getPriceFromUniswap(uint32 _seconds, uint128 _amount) internal view virtual returns (uint256 _price) {
84:  function _toBase18(uint256 _amount, uint8 _decimals) internal pure returns (uint256 _e18Amount) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol

```solidity
// @dev/notice missing
179:  function underlying() external view override returns (address _usdaAddress) {
184:  function totalUnderlying() external view override returns (uint256 _usdaAmount) {
190:  function balanceOfUnderlying(address _owner) external view override returns (uint256 _redeemableUSDA) {
196:  function underlyingToWrapper(uint256 _usdaAmount) external view override returns (uint256 _wusdaAmount) {
202:  function wrapperToUnderlying(uint256 _wusdaAmount) external view override returns (uint256 _usdaAmount) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol

```solidity
53:  constructor(

// @param and @return missing
151:  function _totalToFraction(uint256 _total, uint256 _fraction) internal pure returns (uint256 _amount) {
158:  function _claimable(
194:  function _getRate(uint256 _distributedAmph) internal pure returns (uint256 _rate) {
202:  function _calculate(uint256 _tokenAmountToSend) internal view returns (uint256 _amphAmount) {
249:  function _calculate(uint256 _tokenAmountToSend) internal view returns (uint256 _amphAmount) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol

```solidity
39:  event LogRebase(uint256 indexed epoch, uint256 totalSupply);
40:  event LogMonetaryPolicyUpdated(address monetaryPolicy);
51:  modifier onlyMonetaryPolicy() {
56:  modifier validRecipient(address _to) {
95:  constructor(string memory _name, string memory _symbol) {

// @notice/dev missing
111:  function setMonetaryPolicy(address _monetaryPolicy) external onlyOwner {
128:  function balanceOf(address _who) external view override returns (uint256 _balance) {
136:  function scaledBalanceOf(address _who) external view returns (uint256 _balance) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol

```solidity
57:  constructor(IERC20 _sUSDAddr) UFragments('USDA Token', 'USDA') {

// @params missing
101:  function _deposit(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
147:  function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
167:  function _mint(address _target, uint256 _amount) internal {
188:  function _burn(address _target, uint256 _amount) internal {

// @return missing
140:  function withdrawAllTo(address _target) external override returns (uint256 _susdWithdrawn) {
```


https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol

```solidity
// @params missing
320:  function _depositAndStakeOnConvex(address _token, IBooster _booster, uint256 _amount, uint256 _poolId) internal {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
88:  constructor(address _amph) {

// @params missing
507:  function castVoteBySig(uint256 _proposalId, uint8 _support, uint8 _v, bytes32 _r, bytes32 _s) external override {

// @return missing
715:  function timelock() external view override returns (address _timelock) {
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol

```solidity
// @params missing
1015:  function _modifyTotalDeposited(uint256 _amount, address _token, bool _increase) internal {
```

---

### Version [4]

- Pragma versions should be standardized and avoid floating pragma `( ^ )`.

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurveSlave.sol

```solidity
// lock it to a single version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/IChainlinkOracleRelay.sol

```solidity
// lock it to a single version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/IAnchoredViewRelay.sol

```solidity
// lock it to a single version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IWStETH.sol

```solidity
// lock it to a single version
2:  pragma solidity >=0.8.4 <0.9.0;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IRoles.sol

```solidity
// lock it to a single version
2:  pragma solidity >=0.8.4 <0.9.0;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IAMPH.sol

```solidity
// lock it to a single version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICToken.sol

```solidity
// lock it to a single version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICVX.sol

```solidity
// lock it to a single version
2:  pragma solidity >=0.8.4 <0.9.0;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICurveAddressesProvider.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IAmphoraProtocolToken.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IVirtualBalanceRewardPool.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultDeployer.sol

```solidity
// lock it to a single version
2:  pragma solidity >=0.8.4 <0.9.0;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/IOracleRelay.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICurveMaster.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IBaseRewardPool.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IWUSDA.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IAMPHClaimer.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVault.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IGovernorCharlie.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultDeployer.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3TokenOracleRelay.sol

```solidity
// lock it to a single version
2:  pragma solidity >=0.5.0 <0.9.0;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/WstEthOracle.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/GovernanceStructs.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/TriCrypto2Oracle.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol

```solidity
// lock it to a single version
2:  pragma solidity >=0.5.0 <0.9.0;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol

```solidity
// remove the ^ for a locked and safer version
3:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol

```solidity
// remove the ^ for a locked and safer version
2:pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

```solidity
// remove the ^ for a locked and safer version
3:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol

```solidity
// remove the ^ for a locked and safer version
2:  pragma solidity ^0.8.9;
```
