# Gas Optamization
  ## Note:  The last 5 types were not found completely by the bots {30,31,32,33,34,35}
   I found that it was missed by the bots
# SUMMRY
|      |    ISSUE     |  INSTANCE   |
|------|--------------|-------------|
|[G-01]| Can Make The Variable Outside The Loop To Save Gas |13|
|[G-02]| Use do while loops instead of for loops|1|
|[G-03]| Use assembly to write address storage values|9|
|[G-04]| Use nested if statements instead of &&|7|
|[G-05]| Make 3 event parameters indexed when possible|30|
|[G-06]| Use assembly to perform efficient back-to-back calls|2|
|[G-07]| public functions to external|9|
|[G-08]| Expressions for constant values such as a call to keccak256(), should use immutable rather than constant|5|
|[G-09]| internal functions not called by the contract should be removed to save deployment gas|3|
|[G-10]| Amounts should be checked for 0 before calling a transfer|7|
|[G-11]| Do not calculate constants|8|
|[G-12]| State variables can be packed to use fewer storage slots|1|
|[G-13]| Gas savings can be achieved by changing the model for assigning value to the structure |1|
|[G-14]| Use != 0 instead of > 0 for unsigned integer comparison|8|
|[G-15]| Use hardcode address instead address(this)|8|
|[G-16]| use Mappings Instead of Arrays|2|
|[G-17]| Duplicated require()/if() checks should be refactored to a modifier or function|5|
|[G-18]| Not using the named return variables when a function returns, wastes deployment gas|6|
|[G-19]| Unnecessary computation|14|
|[G-20]| keccak256() should only need to be called on a specific string literal once|4|
|[G-21]| array[index] += amount is cheaper than array[index] = array[index] + amount (or related variants)|7|
|[G-22]| Avoid emitting storage values|16|
|[G-23]| Use uint256(1)/uint256(2) instead for true and false boolean states|6|
|[G-24]| Using XOR (^) and OR (|) bitwise equivalents|8|
|[G-25]| When possible, use assembly instead of unchecked{++i}|10|
|[G-26]| Use assembly for math (add, sub, mul, div)|27|
|[G-27]| Refactor event to avoid emitting data that is already present in transaction data|4|
|[G-28]| Refactor event to avoid emitting empty data|4|
|[G-29]| Sort array offchain to check duplicates in O(n) instead of O(n^2)|1|
|[G-30]| Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate|4|
|[G-31]| Use of memory instead of storage for struct/array state variables|2|
|[G-32]| Multiple accesses of a mapping/array should use a local variable cache|5|
|[G-32]| Using bools for storage incurs overhead|1|
|[G-34]| Using pre instead of post increments/decrements|1|
|[G-35]| Sort Solidity operations using short-circuit mode|1|

## [G-01] Can Make The Variable Outside The Loop To Save Gas 
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
```solidity
File:  core/solidity/contracts/core/Vault.sol
177   uint256 _crvReward = _rewardsContract.earned(address(this));

187   uint256 _extraRewards = _rewardsContract.extraRewardsLength();

209   (uint256 _takenCVX, uint256 _takenCRV, uint256 _claimableAmph) =
          _amphClaimer.claimable(address(this), this.id(), _totalCvxReward, _totalCrvReward);

257   uint256 _earnedReward = _virtualReward.earned(address(this));          
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L177

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
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L874


## [G-02] Use do while loops instead of for loops
A do while loop will cost less gas since the condition is not being checked for the first iteration.

```solidity
File:  core/solidity/contracts/core/AMPHClaimer.sol
203 if (_tokenAmountToSend == 0) return 0;

    uint256 _tempAmountReceived = _tokenAmountToSend; // CRV, 1e18
    uint256 _amphToMint; // 1e18

    uint256 _distributedAmph = distributedAmph;

    while (_tempAmountReceived > 0) {
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L203-L210


## [G-03] Use assembly to write address storage values

By using assembly to write to address storage values, you can bypass some of these operations and lower the gas cost of writing to storage. Assembly code allows you to directly access the Ethereum Virtual Machine (EVM) and perform low-level operations that are not possible in Solidity.

example of using assembly to write to address storage values:
```
contract MyContract {
    address private myAddress;
    
    function setAddressUsingAssembly(address newAddress) public {
        assembly {
            sstore(0, newAddress)
        }
    }
}
```

```solidity
File:  core/solidity/contracts/core/AMPHClaimer.sol
61    vaultController = IVaultController(_vaultController);

120   vaultController = IVaultController(_newVaultController);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L61

```solidity
File:  core/solidity/contracts/core/USDA.sol
64   pauser = _pauser;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L64

```solidity
File:  core/solidity/contracts/core/WUSDA.sol
49   USDA = _usdaToken;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L49

```solidity
File:  core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol
44    anchorRelay = IOracleRelay(_anchorAddress);

46     mainRelay = IOracleRelay(_mainAddress);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L44

```solidity
File:  core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol
40   _AGGREGATOR = AggregatorV2V3Interface(_feedAddress);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L40

```solidity
File:  core/solidity/contracts/periphery/oracles/CTokenOracle.sol
21   cToken = ICToken(_cToken);

58    anchoredViewUnderlying = IOracleRelay(_anchoredViewUnderlying);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L21

```solidity
File:  core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol
42    LOOKBACK = _lookback;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L42


## [G-04]Use nested if statements instead of &&
If the if statement has a logical AND and is not followed by an else statement, it can be replaced with 2 if statements.

```
contract NestedIfTest {

    //Execution cost: 22334 gas
    function funcBad(uint256 input) public pure returns (string memory) { 
       if (input<10 && input>0 && input!=6){ 
           return "If condition passed";
       } 

   }

    //Execution cost: 22294 gas
    function funcGood(uint256 input) public pure returns (string memory) { 
    if (input<10) { 
        if (input>0){
            if (input!=6){
                return "If condition passed";
            }
        }
    }
   }
}
   
```
```solidity
File:  core/solidity/contracts/core/AMPHClaimer.sol
88    if (_crvAmountToSend != 0 && _claimedAmph != 0) distributedAmph += (_claimedAmph / 1e12); // scale back to 1e6
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L88

```solidity
File:  core/solidity/contracts/core/Vault.sol
158     if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L158

```solidity
File:  core/solidity/contracts/core/VaultController.sol
1018    if (_increase && (_collateral.totalDeposited + _amount) > _collateral.cap) revert VaultController_CapReached();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L1018



```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
170    if (amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender)) {

208    if (_emergency && !isWhitelisted(msg.sender)) {        
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L170

```solidity
File:  core/solidity/contracts/utils/ThreeLines0_100.sol
34   if (!((0 < _r2) && (_r2 < _r1) && (_r1 < _r0))) revert ThreeLines0_100_InvalidCurve();

35   if (!((0 < _s1) && (_s1 < _s2) && (_s2 < 1e18))) revert ThreeLines0_100_InvalidBreakpointValues();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/ThreeLines0_100.sol#L34

## [G-05] Make 3 event parameters indexed when possible
 events are used to emit information about state changes in a contract. When defining an event, it's important to consider the gas cost of emitting the event, as well as the efficiency of searching for events in the Ethereum blockchain.

Making event parameters indexed can help reduce the gas cost of emitting events and improve the efficiency of searching for events. When an event parameter is marked as indexed, its value is stored in a separate data structure called the event topic, which allows for more efficient searching of events.
Exmple:
```
• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes value);

• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes indexed value);
```

```solidity
File:  core/solidity/contracts/utils/UFragments.sol
39  event LogRebase(uint256 indexed epoch, uint256 totalSupply);

40  event LogMonetaryPolicyUpdated(address monetaryPolicy);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L39

```solidity
File: core/solidity/interfaces/core/IAMPHClaimer.sol
20    event ClaimedAmph(
    address indexed _vaultClaimer, uint256 _cvxTotalRewards, uint256 _crvTotalRewards, uint256 _amphAmount
  );

36   event RecoveredDust(address indexed _token, address _receiver, uint256 _amount);

42    event ChangedCvxRewardFee(uint256 _newCvxReward);

48     event ChangedCrvRewardFee(uint256 _newCrvReward);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IAMPHClaimer.sol#L20

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
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IUSDA.sol#L19

```solidity
File:  core/solidity/interfaces/core/IVault.sol
20     event Deposit(address _token, uint256 _amount);

27       event Withdraw(address _token, uint256 _amount);

34     event ClaimedReward(address _token, uint256 _amount);

41      event Staked(address _token, uint256 _amount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IVault.sol#L20

```solidity
File:  core/solidity/interfaces/core/IVaultController.sol
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
File:  core/solidity/interfaces/periphery/ICurveMaster.sol
16     event VaultControllerSet(address _oldVaultControllerAddress, address _newVaultControllerAddress);

24      event CurveSet(address _oldCurveAddress, address _token, address _newCurveAddress);

32      event CurveForceSet(address _oldCurveAddress, address _token, address _newCurveAddress);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/periphery/ICurveMaster.sol#L32



## [G-06] Use assembly to perform efficient back-to-back calls
If a similar external call is performed back-to-back, we can use assembly to reuse any function signatures and function parameters that stay the same. In addition, we can also reuse the same memory space for each function call (scratch space + free memory pointer + zero slot), which can potentially allow us to avoid memory expansion costs.
Note: In order to do this optimization safely we will cache the free memory pointer value and restore it once we are done with our function calls. We will also set the zero slot back to 0 if neccessary.
[Reffrence](https://code4rena.com/reports/2023-05-ajna#g-10-use-assembly-to-perform-efficient-back-to-back-calls)


```solidity
File:  core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol
50    BASE_TOKEN_DECIMALS = IERC20Metadata(BASE_TOKEN).decimals();

51    QUOTE_TOKEN_DECIMALS = IERC20Metadata(QUOTE_TOKEN).decimals();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L50

## [G-07] public functions to external
External call cost is less expensive than of public functions.
Contracts are allowed to override their parents’ functions and change the visibility from external to public.

```solidity
File:   core/solidity/contracts/core/VaultController.sol
278    function mintVault() public override whenNotPaused returns (address _vaultAddress) {    

988   function vaultSummaries(
    uint96 _start,
    uint96 _stop
  ) public view override returns (VaultSummary[] memory _vaultSummaries) {
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L278

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
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

```solidity
File:   core/solidity/contracts/utils/UFragments.sol
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

## [G-08] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

The reason for this is that constant variables are evaluated at runtime and their value is included in the bytecode of the contract. This means that any expensive operations performed as part of the constant expression, such as a call to keccak256(), will be executed every time the contract is deployed, even if the result is always the same. This can result in higher gas costs.

In contrast, immutable variables are evaluated at compilation time, and their values are included in the bytecode of the contract as constants. This means that any expensive operations performed as part of the immutable expression are only executed once, when the contract is compiled, and the result is reused every time the contract is deployed. This can result in lower gas costs compared to using constant variables.

Let's consider an example to illustrate this. Suppose we want to store the hash of a string as a constant value in our contract. We could do this using a constant variable, like so:

```
bytes32 constant MY_HASH = keccak256("my string");
```
Alternatively, we could use an immutable variable, like so:
```
bytes32 immutable MY_HASH = keccak256("my string");
```

```solidity
File:  core/solidity/contracts/core/USDA.sol
21     bytes32 public constant VAULT_CONTROLLER_ROLE = keccak256('VAULT_CONTROLLER');
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L21

```solidity
File:   core/solidity/contracts/governance/GovernorCharlie.sol
19   bytes32 public constant DOMAIN_TYPEHASH =
    keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

23     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L23

```solidity
File:  core/solidity/contracts/utils/UFragments.sol
87   bytes32 public constant EIP712_DOMAIN =
    keccak256('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)');

89    bytes32 public constant PERMIT_TYPEHASH =
    keccak256('Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)');    
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L87


## [G-09] internal functions not called by the contract should be removed to save deployment gas
When you define an internal function in a Solidity contract, Solidity generates code for that function and includes it in the contract bytecode. If the function is not called by the contract itself or any of its external functions, then 
```solidity
File:   core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol
12     function _getLpAddress(address _crvPool) internal view returns (address _lpAddress) {
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol#L12


```solidity
File:  core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol
48   function _getVirtualPrice() internal view override returns (uint256 _value) {  
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol#L48

```solidity
File:  core/solidity/contracts/periphery/oracles/OracleRelay.sol
19     function _setUnderlying(address _underlying) internal {
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L19


## [G-10] Amounts should be checked for 0 before calling a transfer
It is generally a good practice to check for zero values before making any transfers in smart contract functions. This can help to avoid unnecessary external calls and can save gas costs.

Checking for zero values is especially important when transferring tokens or ether, as sending these assets to an address with a zero value will result in the loss of those assets.

In Solidity, you can check whether a value is zero by using the == operator. Here's an example of how you can check for a zero value before making a transfer:

```
function transfer(address payable recipient, uint256 amount) public {
    require(amount > 0, "Amount must be greater than zero");
    recipient.transfer(amount);
}
```
In the above example, we check to make sure that the amount parameter is greater than zero before making the transfer to the recipient address. If the amount is zero or negative, the function will revert and the transfer will not be made.


```solidity
File:  core/solidity/contracts/core/AMPHClaimer.sol
129       IERC20(_token).transfer(owner(), _amount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L29

```solidity
File:   core/solidity/contracts/core/USDA.sol
216    sUSD.transfer(_to, _amount);

246    sUSD.transfer(_target, _susdAmount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L216

```solidity
File:   core/solidity/contracts/core/Vault.sol
129       SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_tokenAddress), _msgSender(), _amount);

285         SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_token), _to, _amount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#129

```solidity
File:  core/solidity/contracts/core/WUSDA.sol
215     IERC20(USDA).safeTransferFrom(_from, address(this), _usdaAmount);

228     IERC20(USDA).safeTransfer(_to, _usdaAmount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L215

## [G-11] Do not calculate constants
Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas

```solidity
File:  core/solidity/contracts/core/AMPHClaimer.sol
18     uint256 internal constant _FIFTY_MILLIONS = 50_000_000 * 1e6;

21     uint256 internal constant _TWENTY_FIVE_THOUSANDS = 25_000 * 1e6;

24     uint256 internal constant _FIFTY = 50 * 1e6;

27     uint256 public constant BASE_SUPPLY_PER_CLIFF = 8_000_000 * 1e6;

30     uint256 public constant TOTAL_CLIFFS = 1000;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L18

```solidity
File:   core/solidity/contracts/core/WUSDA.sol
35     uint256 public constant MAX_wUSDA_SUPPLY = 10_000_000 * (10 ** 18); // 10 M
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L35

```solidity
File:  core/solidity/contracts/utils/UFragments.sol
62   uint256 private constant MAX_UINT256 = 2 ** 256 - 1;

63   uint256 private constant INITIAL_FRAGMENTS_SUPPLY = 1 * 10 ** DECIMALS;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L62

## [G-12] State variables can be packed to use fewer storage slots
The EVM works with 32 byte words. Variables less than 32 bytes can be declared next to eachother in storage and this will pack the values together into a single 32 byte storage slot (if the values combined are <= 32 bytes). If the variables packed together are retrieved together in functions we will effectively save ~2000 gas with every subsequent SLOAD for that storage slot. This is due to us incurring a Gwarmaccess (100 gas) versus a Gcoldsload (2100 gas)

```solidity
File:  core/solidity/contracts/core/VaultController.sol
  uint8 public constant MAX_DECIMALS = 18;

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
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L25-L71


## [G-13] Gas savings can be achieved by changing the model for assigning value to the structure 
Here's an example of how you can use the memory keyword with a struct:

```
struct MyStruct {
    uint256 variable1;
    uint256 variable2;
}

function myFunction() public {
    MyStruct storge/memory myStruct;
    myStruct.variable1 = 123;
    myStruct.variable2 = 456;

    // do something with myStruct in memory
}
```
```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
187      Proposal memory _newProposal = Proposal({
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L187



## [G-14]Use != 0 instead of > 0 for unsigned integer comparison
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


```solidity
File:  core/solidity/contracts/core/Vault.sol
206    if (_totalCrvReward > 0 || _totalCvxReward > 0) {

223    if (_totalCvxReward > 0) CVX.transfer(_msgSender(), _totalCvxReward);

224    if (_totalCrvReward > 0) CRV.transfer(_msgSender(), _totalCrvReward);

276    if (_cvxReward > 0) _rewards[1].amount = _cvxReward - _takenCVX;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L206

```solidity
File:   core/solidity/contracts/core/VaultController.sol
558       if (_fee > 0) usda.vaultControllerMint(owner(), _fee);

660       if (_tokenAmount > 0) _tokensToLiquidate = _tokenAmount;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L558

```solidity
File:    core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol
77       require(_mainValue > 0, 'invalid oracle value');

80       require(_anchorPrice > 0, 'invalid anchor value');
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L77

## [G-15] Use hardcode address instead address(this)
it can be more gas-efficient to use a hardcoded address instead of the address(this) expression, especially if you need to use the same address multiple times in your contract.

The reason for this is that using address(this) requires an additional EXTCODESIZE operation to retrieve the contract's address from its bytecode, which can increase the gas cost of your contract. By pre-calculating and using a hardcoded address, you can avoid this additional operation and reduce the overall gas cost of your contract.

Here's an example of how you can use a hardcoded address instead of address(this):

```
contract MyContract {
    address public myAddress = 0x1234567890123456789012345678901234567890;
    
    function doSomething() public {
        // Use myAddress instead of address(this)
        require(msg.sender == myAddress, "Caller is not authorized");
        
        // Do something
    }
}
```
In the above example, we have a contract MyContract with a public address variable myAddress. Instead of using address(this) to retrieve the contract's address, we have pre-calculated and hardcoded the address in the variable. This can help to reduce the gas cost of our contract and make our code more efficient.
[References](https://book.getfoundry.sh/reference/forge-std/compute-create-address)


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
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L177


## [G-16]use Mappings Instead of Arrays
Arrays are useful when you need to maintain an ordered list of data that can be iterated over, but they have a higher gas cost for read and write operations, especially when the size of the array is large. This is because Solidity needs to iterate over the entire array to perform certain operations, such as finding a specific element or deleting an element.
Mappings, on the other hand, are useful when you need to store and access data based on a key, rather than an index. Mappings have a lower gas cost for read and write operations, especially when the size of the mapping is large, since Solidity can perform these operations based on the key directly, without needing to iterate over the entire data structure.

```solidity
File:  core/solidity/contracts/core/VaultController.sol
46    address[] public enabledTokens;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L46

```solidity
File:  core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol
17     IOracleRelay[] public anchoredUnderlyingTokens;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#17


## [G-17] Duplicated require()/if() checks should be refactored to a modifier or function
sing modifiers or functions can make your code more gas-efficient by reducing the overall number of operations that need to be executed. For example, if you have a complex validation check that involves multiple operations, and you refactor it into a function, then the function can be executed with a single opcode, rather than having to execute each operation separately in multiple locations.

Recommendation
You can consider adding a modifier like below
```
 modifer check (address checkToAddress) {    
     require(checkToAddress != address(0) && checkToAddress != SENTINEL_MODULES, "BSA101");  
      _; 
 }
```

```solidity
File:    core/solidity/contracts/core/USDA.sol
102       if (_susdAmount == 0) revert USDA_ZeroAmount();

149       if (_susdAmount == 0) revert USDA_ZeroAmount();

162       if (_susdAmount == 0) revert USDA_ZeroAmount();

183       if (_susdAmount == 0) revert USDA_ZeroAmount();

203       if (_susdAmount == 0) revert USDA_ZeroAmount();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L102

## [G-18]Not using the named return variables when a function returns, wastes deployment gas
When a function returns multiple values without named return variables, it creates a temporary variable to hold the returned values, which can increase the deployment gas cost


```solidity
File:   core/solidity/contracts/utils/UFragments.sol
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


## [G-19] Unnecessary computation
When emitting an event that includes a new and an old value, it is cheaper in gas to avoid caching the old value in memory. Instead, emit the event, then save the new value in storage.

Proof of Concept
Instances include:

```
OwnableProxyDelegation.sol
function _setOwner
Recommended Mitigation
```
Replace
```
address oldOwner = _owner;
_owner = newOwner;
emit OwnershipTransferred(oldOwner, newOwner)
```
with
```
emit OwnershipTransferred(_owner_, newOwner)
_owner = newOwner;
```

```solidity
File:  core/solidity/contracts/core/VaultController.sol
417 IAMPHClaimer _oldClaimerContract = claimerContract;
    claimerContract = _newClaimerContract;

    emit ChangedClaimerContract(_oldClaimerContract, _newClaimerContract);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L417-


```solidity
File:  core/solidity/contracts/core/VaultController.sol
427 uint192 _oldBorrowingFee = initialBorrowingFee;
    initialBorrowingFee = _newBorrowingFee;

    emit ChangedInitialBorrowingFee(_oldBorrowingFee, _newBorrowingFee);    
```    
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L427-L430

```solidity
File:  core/solidity/contracts/core/VaultController.sol
437   uint192 _oldLiquidationFee = liquidationFee;
    liquidationFee = _newLiquidationFee;

    emit ChangedLiquidationFee(_oldLiquidationFee, _newLiquidationFee);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L437-L440

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
595   uint256 _oldEmergencyTimelockDelay = emergencyTimelockDelay;
    emergencyTimelockDelay = _emergencyTimelockDelay;

    emit NewEmergencyDelay(_oldEmergencyTimelockDelay, emergencyTimelockDelay);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L595-L598


```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
606   uint256 _oldVotingDelay = votingDelay;
    votingDelay = _newVotingDelay;

    emit VotingDelaySet(_oldVotingDelay, votingDelay);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L606-L609

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
617   uint256 _oldVotingPeriod = votingPeriod;
    votingPeriod = _newVotingPeriod;

    emit VotingPeriodSet(_oldVotingPeriod, votingPeriod);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L617-L620

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
628  uint256 _oldEmergencyVotingPeriod = emergencyVotingPeriod;
    emergencyVotingPeriod = _newEmergencyVotingPeriod;

    emit EmergencyVotingPeriodSet(_oldEmergencyVotingPeriod, emergencyVotingPeriod);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/#L628-L631

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
639   uint256 _oldProposalThreshold = proposalThreshold;
    proposalThreshold = _newProposalThreshold;

    emit ProposalThresholdSet(_oldProposalThreshold, proposalThreshold);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/#L630-L642

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
650    uint256 _oldQuorumVotes = quorumVotes;
    quorumVotes = _newQuorumVotes;

    emit NewQuorum(_oldQuorumVotes, quorumVotes);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L650-L653

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
661      uint256 _oldEmergencyQuorumVotes = emergencyQuorumVotes;
    emergencyQuorumVotes = _newEmergencyQuorumVotes;

    emit NewEmergencyQuorum(_oldEmergencyQuorumVotes, emergencyQuorumVotes);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L661-L664

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
685    address _oldGuardian = whitelistGuardian;
    whitelistGuardian = _account;

    emit WhitelistGuardianSet(_oldGuardian, whitelistGuardian);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L685-L688

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
696   uint256 _oldOptimisticVotingDelay = optimisticVotingDelay;
    optimisticVotingDelay = _newOptimisticVotingDelay;

    emit OptimisticVotingDelaySet(_oldOptimisticVotingDelay, optimisticVotingDelay);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L696-L699

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
707  uint256 _oldOptimisticQuorumVotes = optimisticQuorumVotes;
    optimisticQuorumVotes = _newOptimisticQuorumVotes;

    emit OptimisticQuorumVotesSet(_oldOptimisticQuorumVotes, optimisticQuorumVotes);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L707-L710

```solidity
File:  core/solidity/contracts/periphery/CurveMaster.sol
32       address _oldCurveAddress = vaultControllerAddress;
    vaultControllerAddress = _vaultMasterAddress;

    emit VaultControllerSet(_oldCurveAddress, _vaultMasterAddress);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/CurveMaster.sol#L32-L35

## [G‑20] keccak256() should only need to be called on a specific string literal once
It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to bytes4 should also only be done once.

```solidity
File:   core/solidity/contracts/governance/GovernorCharlie.sol
19     bytes32 public constant DOMAIN_TYPEHASH =
    keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

23      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L19


```solidity
File:  core/solidity/contracts/utils/UFragments.sol
87    bytes32 public constant EIP712_DOMAIN =
    keccak256('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)');

89     bytes32 public constant PERMIT_TYPEHASH =
    keccak256('Permit(address owner,address spender,uint256 value,uint256 nnoce,uint256 deadline)');    
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L87

## [G-21] array[index] += amount is cheaper than array[index] = array[index] + amount (or related variants)
When updating a value in an array with arithmetic, using array[index] += amount is cheaper than array[index] = array[index] + amount. This is because you avoid an additonal mload when the array is stored in memory, and an sload when the array is stored in storage. This can be applied for any arithmetic operation including +=, -=,/=,*=,^=,&=, %=, <<=,>>=, and >>>=. This optimization can be particularly significant if the pattern occurs during a loop.


```solidity
File:  core/solidity/contracts/utils/UFragments.sol
182    _gonBalances[msg.sender] = _gonBalances[msg.sender] - _gonValue;

183    _gonBalances[_to] = _gonBalances[_to] + _gonValue;

199    _gonBalances[_to] = _gonBalances[_to] + _gonValue;

230    _gonBalances[_from] = _gonBalances[_from] - _gonValue;

231    _gonBalances[_to] = _gonBalances[_to] + _gonValue;

250     _gonBalances[_to] = _gonBalances[_to] + _gonValue;

284    _allowedFragments[msg.sender][_spender] = _allowedFragments[msg.sender][_spender] + _addedValue;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L182

## [G-22]Avoid emitting storage values
Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. We can avoid unecessary SLOADs by caching storage values that were previously accessed and emitting those cached values.

```solidity
File:  core/solidity/contracts/core/Vault.sol
150       emit Staked(_tokenAddress, balances[_tokenAddress]);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L150

```solidity
File:   core/solidity/contracts/core/VaultController.sol
290      emit NewVault(_vaultAddress, vaultsMinted, _msgSender());
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L290

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
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L587

```solidity
File:  core/solidity/contracts/utils/UFragments.sol
105       emit Transfer(address(this), address(0x0), _totalSupply);

286       emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);

301       emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L105

## [G‑23] Use uint256(1)/uint256(2) instead for true and false boolean states
If you don't use boolean for storage you will avoid Gwarmaccess 100 gas. In addition, state changes of boolean from true to false can cost up to ~20000 gas rather than uint256(2) to uint256(1) that would cost significantly less.

```solidity
File:  core/solidity/contracts/core/Vault.sol
102      isTokenStaked[_token] = true;

145      isTokenStaked[_tokenAddress] = true;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L102

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
300      queuedTransactions[_txHash] = true;

312     _proposal.executed = true;

381     _proposal.canceled = true;

547     _receipt.hasVoted = true;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L300


## [G-24] Using XOR (^) and OR (|) bitwise equivalents
Estimated savings: 73 gas
```solidity
File:  core/solidity/contracts/core/Vault.sol
169      if (_crvTotalRewards == 0 || _amphBalance == 0) return (0, 0, 0);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L169

```solidity
File:  core/solidity/contracts/core/Vault.sol
206   if (_totalCrvReward > 0 || _totalCvxReward > 0) {    
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L206


```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
174     _targets.length != _values.length || _targets.length != _signatures.length || _targets.length != _calldatas.length

372      || msg.sender != whitelistGuardian

463     if (proposalCount < _proposalId || _proposalId <= initialProposalId) revert GovernorCharlie_InvalidProposalId();

471    || (!_whitelisted && _proposal.forVotes <= _proposal.againstVotes)
        || (!_whitelisted && _proposal.forVotes < _proposal.quorumVotes)        
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L174

```solidity
File:  core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol
41       _stale = AGGREGATOR.isStale() || BASE_AGGREGATOR.isStale();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L41

```solidity
File:  core/solidity/contracts/utils/UFragments.sol
57       if (_to == address(0) || _to == address(this)) revert UFragments_InvalidRecipient();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L57

## [G-25] When possible, use assembly instead of unchecked{++i}
You can also use unchecked{++i;} for even more gas savings but this will not check to see if i overflows. For best gas savings, use inline assembly, however, this limits the functionality you can achieve.

```
//loop with unchecked{++i}
function uncheckedPlusPlusI() public pure {
    uint256 j = 0;
    for (uint256 i; i < 10; ) {
        j++;
        unchecked {
            ++i;
        }
    }
}
```
Gas: 1329
```
//loop with inline assembly
function inlineAssemblyLoop() public pure {
    assembly {
        let j := 0
        for {
            let i := 0
        } lt(i, 10) {
            i := add(i, 0x01)
        } {
            j := add(j, 0x01)
        }
    }
}
```
Gas: 709




```solidity
File:  core/solidity/contracts/core/USDA.sol
50    unchecked {
        _i++;
      }
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L50

```solidity
File:   core/solidity/contracts/core/Vault.sol
197   unchecked {
          ++_j;
        }

201     unchecked {
        ++_i;
        }

260    unchecked {
        ++_i;
       }
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L


```solidity
File:   core/solidity/contracts/core/VaultController.sol
243   unchecked {
        ++_i;
      }

267   unchecked {
        ++_i;
      }

1001   unchecked {
          ++_j;
        }

1008    unchecked {
        ++_i;
      }                          
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L243

```solidity
File:  core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol
25    unchecked {
        ++_i;
      }

46     unchecked {
        ++_i;
      }
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L25

## [G-26] Use assembly for math (add, sub, mul, div)
Use assembly for math instead of Solidity. You can check for overflow/underflow in assembly to ensure safety.

Addition:
```
//addition in Solidity
function addTest(uint256 a, uint256 b) public pure {
    uint256 c = a + b;
}
```
Gas: 303
```
//addition in assembly
function addAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := add(a, b)
        if lt(c, a) {
            mstore(0x00, "overflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 263
```
Subtraction
```
//subtraction in Solidity
function subTest(uint256 a, uint256 b) public pure {
  uint256 c = a - b;
}
```
Gas: 300
```
//subtraction in assembly
function subAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := sub(a, b)
        if gt(c, a) {
            mstore(0x00, "underflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 263
```
Multiplication
```
//multiplication in Solidity
function mulTest(uint256 a, uint256 b) public pure {
    uint256 c = a * b;
}
```
Gas: 325
```
//multiplication in assembly
function mulAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := mul(a, b)
        if lt(c, a) {
            mstore(0x00, "overflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 265

Division
```
//division in Solidity
function divTest(uint256 a, uint256 b) public pure {
    uint256 c = a * b;
}
```
Gas: 325

```
//division in assembly
function divAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := div(a, b)
        if gt(c, a) {
            mstore(0x00, "underflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 265


```solidity
File:  core/solidity/contracts/core/AMPHClaimer.sol
153      _amount = (_total * _fraction) / _BASE;

195       uint256 _foo = (_TWENTY_FIVE_THOUSANDS * BASE_SUPPLY_PER_CLIFF) / Math.max(_distributedAmph, _FIFTY_MILLIONS);

196       uint256 _bar = (_distributedAmph * 1e12) / (BASE_SUPPLY_PER_CLIFF * _FIFTY);

198       _rate = 1e6 + (_foo - _bar);

222       _amphForThisTurn = ((_rate * _tempAmountReceived) / 1e12) / 1e6; // 1e6

225       uint256 _amphAvailableForThisCliff = (_bottomLastCliff + BASE_SUPPLY_PER_CLIFF) - _distributedAmph; // 1e6

232        uint256 _amountTokenToEnter = (_amphAvailableForThisCliff * 1e18) / _rate;

233       _tempAmountReceived -= _amountTokenToEnter;

239      _distributedAmph += _amphForThisTurn; // 1e6

241      _amphToMint += (_amphForThisTurn * 1e12); // 1e18

250     _cliff = _distributedAmph / BASE_SUPPLY_PER_CLIFF;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L195

```solidity
File:  core/solidity/contracts/core/Vault.sol
181        _totalCrvReward += _crvReward;

183        _totalCvxReward += _calculateCVXReward(_crvReward);

218        _totalCvxReward -= _takenCVX;

219        _totalCrvReward -= _takenCRV;

310         baseLiability += _baseAmount;

314         baseLiability -= _baseAmount;

343        _cvxAmount = (_crv * _reduction) / _totalCliffs;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L181

```solidity
File:   core/solidity/contracts/core/VaultController.sol
271      tokensRegistered += _tokensRegistered;

280      vaultsMinted += 1;

541      totalBaseLiability += _baseAmount;

600      totalBaseLiability -= _baseAmount;

786       uint256 _denominator = _badFillPrice - _ltvDiscount;

790       uint256 _maxTokensToLiquidate = (_usdaToSolvency * 1e18) / _denominator;

886      _borrowPower += _tokenValue;

914      _borrowPower += _tokenValue;

1040     _priceWithDecimals = _price * 10 ** (18 - _decimals);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L271

## [G-27] Refactor event to avoid emitting data that is already present in transaction data
[Reffrence](https://code4rena.com/reports/2023-05-ajna#g-11-refactor-event-to-avoid-emitting-data-that-is-already-present-in-transaction-data)

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
225    emit ProposalCreatedIndexed(
      _newProposal.id,
      msg.sender,
      _targets,
      _values,
      _signatures,
      _calldatas,
      _newProposal.startBlock, // @audit: present in tx data
      _newProposal.endBlock,   // @audit: present in tx data
      _description
    );

    emit ProposalCreated(
      _newProposal.id,
      msg.sender,
      _targets,
      _values,
      _signatures,
      _calldatas,
      _newProposal.startBlock,  // @audit: present in tx data
      _newProposal.endBlock,    // @audit: present in tx data
      _description
    );
 
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L225-L247

## [G-28] Refactor event to avoid emitting empty data

[Reffrence](https://code4rena.com/reports/2023-05-ajna#g-12-refactor-event-to-avoid-emitting-empty-data)


```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
487    emit VoteCastIndexed(msg.sender, _proposalId, _support, _numberOfVotes, '');

488    emit VoteCast(msg.sender, _proposalId, _support, _numberOfVotes, '');

520    emit VoteCastIndexed(_signatory, _proposalId, _support, _numberOfVotes, '');

521    emit VoteCast(_signatory, _proposalId, _support, _numberOfVotes, '');
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L487

## [G-29] Sort array offchain to check duplicates in O(n) instead of O(n^2)
In the vaultSummaries function, sorting the enabledTokens array off-chain before iterating over it can reduce the gas cost of retrieving token balances for each enabled token in each vault. This is because sorting an array off-chain is generally less expensive than sorting an array on-chain.
[Reffrence](https://code4rena.com/reports/2023-05-ajna#g-13-sort-array-offchain-to-check-duplicates-in-on-instead-of-on2)

```solidity
File:  core/solidity/contracts/core/VaultController.sol
988   function vaultSummaries(
    uint96 _start,
    uint96 _stop
  ) public view override returns (VaultSummary[] memory _vaultSummaries) {
    if (_stop > vaultsMinted) _stop = vaultsMinted;
    _vaultSummaries = new VaultSummary[](_stop - _start + 1);
    for (uint96 _i = _start; _i <= _stop;) {
      IVault _vault = _getVault(_i);
      uint256[] memory _tokenBalances = new uint256[](enabledTokens.length);

      for (uint256 _j; _j < enabledTokens.length;) {
        _tokenBalances[_j] = _vault.balances(enabledTokens[_j]);

        unchecked {
          ++_j;
        }
      }
      _vaultSummaries[_i - _start] =
        VaultSummary(_i, _peekVaultBorrowingPower(_vault), this.vaultLiability(_i), enabledTokens, _tokenBalances);

      unchecked {
        ++_i;
      }
    }
  }
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L988-L1012

### Recommend code 
```solidity
 function vaultSummaries(uint96 _start, uint96 _stop) public view override returns (VaultSummary[] memory _vaultSummaries) {
    if (_stop > vaultsMinted) _stop = vaultsMinted;
    _vaultSummaries = new VaultSummary[](_stop - _start + 1);

    // Sort the enabledTokens array off-chain
    uint256[] memory sortedTokens = new uint256[](enabledTokens.length);
    for (uint256 i = 0; i < enabledTokens.length; i++) {
        sortedTokens[i] = enabledTokens[i];
    }
    quickSort(sortedTokens, uint256(0), enabledTokens.length - 1);

    for (uint96 _i = _start; _i <= _stop;) {
        IVault _vault = _getVault(_i);
        uint256[] memory _tokenBalances = new uint256[](enabledTokens.length);

        // Retrieve token balances for each enabled token in the sorted array
        for (uint256 _j = 0; _j < sortedTokens.length; _j++) {
            _tokenBalances[_j] = _vault.balances(sortedTokens[_j]);
        }

        _vaultSummaries[_i - _start] = VaultSummary(
            _i,
            _peekVaultBorrowingPower(_vault),
            this.vaultLiability(_i),
            sortedTokens,
            _tokenBalances
        );

        _i++;
    }

    return _vaultSummaries;
}

function quickSort(uint256[] memory arr, uint256 left, uint256 right) internal pure {
    if (left < right) {
        uint256 pivotIndex = partition(arr, left, right);
        quickSort(arr, left, pivotIndex - 1);
        quickSort(arr, pivotIndex + 1, right);
    }
}

function partition(uint256[] memory arr, uint256 left, uint256 right) internal pure returns (uint256) {
    uint256 pivotValue = arr[right];
    uint256 i = left;

    for (uint256 j = left; j < right; j++) {
        if (arr[j] < pivotValue) {
            (arr[i], arr[j]) = (arr[j], arr[i]);
            i++;
        }
    }

    (arr[i], arr[right]) = (arr[right], arr[i]);
    return i;
}
```

## [G-30] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate
If both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

```solidity
File:  core/solidity/contracts/core/Vault.sol
43     mapping(address => uint256) public balances;

46     mapping(address => bool) public isTokenStaked;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L43-L46

```solidity
File:  core/solidity/contracts/core/VaultController.sol
    mapping(address => uint96[]) public walletVaultIDs;

  /// @dev Mapping of token address to collateral info
  mapping(address => CollateralInfo) public tokenAddressCollateralInfo;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L40-L43


## [G-31] Use of memory instead of storage for struct/array state variables
When fetching data from storage, using the memory keyword to assign it to a variable reads all fields of the struct/array and incurs a Gcoldsload (2100 gas) for each field.

This can be avoided by declaring the variable with the storage keyword and caching the necessary fields in stack variables.

The memory keyword should only be used if the entire struct/array is being returned or passed to a function that requires memory, or if it is being read from another memory array/struct.


```solidity
File:  core/solidity/contracts/core/VaultController.sol
729       CollateralInfo memory _collateral = tokenAddressCollateralInfo[_assetAddress];

751       CollateralInfo memory _collateral = tokenAddressCollateralInfo[_assetAddress];
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L729


## [G‑32] Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping’s value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. Caching an array’s struct avoids recalculating the array offsets into memory/calldata.


```solidity
File:  core/solidity/contracts/core/Vault.sol
103       _depositAndStakeOnConvex(_token, _booster, balances[_token] + _amount, _poolId);

106       balances[_token] += _amount;





142       if (balances[_tokenAddress] == 0) revert Vault_TokenZeroBalance();

148      _depositAndStakeOnConvex(_tokenAddress, _booster, balances[_tokenAddress], _poolId);

150      emit Staked(_tokenAddress, balances[_tokenAddress]);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L103

## [G-33] Using bools for storage incurs overhead
  
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.

    Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

```solidity
File:  core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol
17     bool public immutable QUOTE_TOKEN_IS_TOKEN0;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L17

## [G-34] Using pre instead of post increments/decrements
Pre increments/decrements (++i/--i) are cheaper than post increments/decrements (i++/i--): it saves 6 gas per expression.

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
186       proposalCount++;
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L186

## [G-35] Sort Solidity operations using short-circuit mode
Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 
//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
370   if (
          (amph.getPriorVotes(_proposal.proposer, (block.number - 1)) >= proposalThreshold)
            || msg.sender != whitelistGuardian
        ) revert GovernorCharlie_WhitelistedProposer();
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L370-L374