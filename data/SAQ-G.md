## Summary

### Gas Optimization

no | Issue |Instances||
|-|:-|:-:|:-:|
| [G-01] | Expressions for constant values such as a call to keccak256(), should use immutable rather than constant | 4 | - |
| [G-02] | Should use arguments instead of state variable | 7 | - |
| [G-03] | State variable can be packed into fewer storage slots | 1 | - |
| [G-04] | Duplicated require()/if() checks should be refactored to a modifier or function | 6 | - |
| [G-05] | Use nested if and, avoid multiple check combinations | 6 | - |
| [G-06] | Use assembly to write address storage values | 4 | - |
| [G-07] | Do not calculate  constant | 7 | - |
| [G-08] | Use hardcode address instead of address(this) | 9 | - |
| [G-09] | Non efficient zero initialization | 3 | - |
| [G-10] | Variable names that consist of all capital letters should be reserved for constant/immutable variables | 1 | - |
| [G-11] | array[index] += amount is cheaper than array[index] = array[index] + amount (or related variants) | 5 | - |
| [G-12] | Use uint256(1)/uint256(2) instead for true and false boolean states | 4 | - |
| [G-13] | Using Bools For Storage Incurs Overhead | 2 | - |
| [G-14] | bytes.concat() can be used in place of abi.encodePacked | 3 | - |
| [G-15] | Use assembly for math (add, sub, mul, div) | 7 | - |
| [G-16] | Refactor event to avoid emitting empty data | 4 | - |


## Gas Optimizations  

## [G-1] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use theright tool for the task at hand. There is a difference between constant variables and immutable variables, and they shouldeach be used in their appropriate contexts.constants should be used for literal values written into the code, and immutablevariables should be used for expressions, or values calculated in, or passed into the constructor.

```solidity
file: /contracts/governance/GovernorCharlie.sol

19      bytes32 public constant DOMAIN_TYPEHASH =
20          keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

23      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L19

```solidity
File: /contracts/utils/UFragments.sol

88     keccak256('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)');

89     bytes32 public constant PERMIT_TYPEHASH =
90     keccak256('Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)');

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L88


## [G-2] Should use arguments instead of state variable 

state variables should not used in emit  ,  This will save near 97 gas   

```solidity
file: /contracts/core/VaultController.sol

///@audit ' vaultsMinted ' is state variable,  it should be cached to stack.


290    emit NewVault(_vaultAddress, vaultsMinted, _msgSender());

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L290


```solidity
file: /contracts/governance/GovernorCharlie.sol

///@audit ' proposalTimelockDelay ' is state variable,  it should be cached to stack.

587    emit NewDelay(_oldTimelockDelay, proposalTimelockDelay); 


///@audit ' votingDelay ' is state variable,  it should be cached to stack.

609    emit VotingDelaySet(_oldVotingDelay, votingDelay);

///@audit ' emergencyTimelockDelay ' is state variable,  it should be cached to stack.


598    emit NewEmergencyDelay(_oldEmergencyTimelockDelay, emergencyTimelockDelay);

///@audit ' proposalThreshold ' is state variable,  it should be cached to stack.


642    emit ProposalThresholdSet(_oldProposalThreshold, proposalThreshold);

///@audit ' votingPeriod ' is state variable,  it should be cached to stack.


620    emit VotingPeriodSet(_oldVotingPeriod, votingPeriod);

///@audit ' votingPeriod ' is state variable,  it should be cached to stack.

653    emit NewQuorum(_oldQuorumVotes, quorumVotes);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L587


## [G-3] State variable can be packed into fewer storage slots					

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas).
Reads of the variables are also cheaper.

```solidity
file: /contracts/core/VaultController.sol

61  uint96 public vaultsMinted;

63  uint256 public tokensRegistered;

65  uint192 public totalBaseLiability;

67  uint192 public protocolFee;

69  uint192 public initialBorrowingFee;

71  uint192 public liquidationFee;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L61

### Recommended Code

```solidity

    uint96 public vaultsMinted;

    uint192 public totalBaseLiability;

    uint192 public protocolFee;

    uint192 public initialBorrowingFee;

    uint192 public liquidationFee;

 +  uint256 public tokensRegistered;
```


## [G-4] Duplicated require()/if() checks should be refactored to a modifier or function

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
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L355


```solidity 
file: /contracts/governance/GovernorCharlie.sol

107    if (msg.sender != address(this)) revert GovernorCharlie_NotGovernorCharlie();

335    if (msg.sender != address(this)) revert GovernorCharlie_NotGovernorCharlie();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L107


```solidity
file: /core/solidity/contracts/core/Vault.sol

118    if (CONTROLLER.tokenId(_tokenAddress) == 0) revert Vault_TokenNotRegistered();

235    if (CONTROLLER.tokenId(_tokenAddress) == 0) revert Vault_TokenNotRegistered();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L118


## [G-5] Use nested if and, avoid multiple check combinations

Using nested if is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.


```solidity
file: /contracts/core/Vault.sol

158    if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L158


```solidity
file: /contracts/core/VaultController.sol

1018    if (_increase && (_collateral.totalDeposited + _amount) > _collateral.cap) revert VaultController_CapReached();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L1018


```solidity
file: /contracts/governance/GovernorCharlie.sol

170    if (amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender)) {


469    else if (
470      (_whitelisted && _proposal.againstVotes > _proposal.quorumVotes)
471        || (!_whitelisted && _proposal.forVotes <= _proposal.againstVotes)
472        || (!_whitelisted && _proposal.forVotes < _proposal.quorumVotes)    

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L170


```solidity
file: /contracts/utils/ThreeLines0_100.sol

34    if (!((0 < _r2) && (_r2 < _r1) && (_r1 < _r0))) revert ThreeLines0_100_InvalidCurve();

35    if (!((0 < _s1) && (_s1 < _s2) && (_s2 < 1e18))) revert ThreeLines0_100_InvalidBreakpointValues();


```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol#L34



## [G-6] Use assembly to write address storage values

By using assembly to write to address storage values, you can bypass some of these operations and lower the gas cost of writing to storage. Assembly code allows you to directly access the Ethereum Virtual Machine (EVM) and perform low-level operations that are not possible in Solidity.

example of using assembly to write to address storage values:
```solidity
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
file: /contracts/governance/GovernorCharlie.sol

686    whitelistGuardian = _account;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L686

```solidity
file: /contracts/core/USDA.sol

64    pauser = _pauser;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L64

```solidity
file: /contracts/periphery/CurveMaster.sol

33    vaultControllerAddress = _vaultMasterAddress;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L33

```solidity
file: /contracts/periphery/oracles/OracleRelay.sol

20    underlying = _underlying;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L20


## [G-7] Do not calculate  constant

When you define a constant in Solidity, the compiler can calculate its value at compile-time and replace all references to the constant with its computed value. This can be helpful for readability and reducing the size of the compiled code, but it can also increase gas usage at runtime.

```solidity
file: /contracts/utils/UFragments.sol

62   uint256 private constant MAX_UINT256 = 2 ** 256 - 1;

63   uint256 private constant INITIAL_FRAGMENTS_SUPPLY = 1 * 10 ** DECIMALS;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L62

```solidity
file: /contracts/core/AMPHClaimer.sol

18  uint256 internal constant _FIFTY_MILLIONS = 50_000_000 * 1e6;

21  uint256 internal constant _TWENTY_FIVE_THOUSANDS = 25_000 * 1e6;

24  uint256 internal constant _FIFTY = 50 * 1e6;

27  uint256 public constant BASE_SUPPLY_PER_CLIFF = 8_000_000 * 1e6;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L18

```solidity
file: /contracts/core/WUSDA.sol

35  uint256 public constant MAX_wUSDA_SUPPLY = 10_000_000 * (10 ** 18); // 10 M

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L35


## [G-8] Use hardcode address instead of address(this)
 
 Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.
References: https://book.getfoundry.sh/reference/forge-std/compute-create-address

Here's an example :
```solidity
contract MyContract {
    address constant public CONTRACT_ADDRESS = 0x1234567890123456789012345678901234567890;
    
    function getContractAddress() public view returns (address) {
        return CONTRACT_ADDRESS;
    }
}
```


```solidity
file: /contracts/governance/GovernorCharlie.sol

///@audit   firstly we should assign hardcode address to variable after that we can use variable instead of address(this).


107    if (msg.sender != address(this)) revert GovernorCharlie_NotGovernorCharlie();

335    if (msg.sender != address(this)) revert GovernorCharlie_NotGovernorCharlie();

509      keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(NAME)), _getChainIdInternal(), address(this)));

716    _timelock = address(this);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L107

```solidity
file: /contracts/core/Vault.sol

92    SafeERC20Upgradeable.safeTransferFrom(IERC20Upgradeable(_token), _msgSender(), address(this), _amount);

191        uint256 _earnedReward = _virtualReward.earned(address(this));

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L92

```solidity
file: /contracts/core/USDA.sol

215    uint256 _amount = sUSD.balanceOf(address(this)) - reserveAmount;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L215

```solidity
file: /contracts/utils/UFragments.sol

105    emit Transfer(address(this), address(0x0), _totalSupply);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L105

```solidity
file: /contracts/core/WUSDA.sol

215    IERC20(USDA).safeTransferFrom(_from, address(this), _usdaAmount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L215


## [G-9] Non efficient zero initialization
 
Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas.

```solidity
file: /contracts/governance/GovernorCharlie.sol

259    for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

313    for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

382    for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L259


## [G-10] Variable names that consist of all capital letters should be reserved for constant/immutable variables

If the variable needs to be different based on which class it comes from, a view/pure function should be used instead 

```solidity
file: /contracts/utils/UFragments.sol

70  uint256 public MAX_SUPPLY; // = type(uint128).max; // (2^128) - 1

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L70


## [G-11] array[index] += amount is cheaper than array[index] = array[index] + amount (or related variants)

When updating a value in an array with arithmetic, using array[index] += amount is cheaper than array[index] = array[index] + amount. This is because you avoid an additonal mload when the array is stored in memory, and an sload when the array is stored in storage. This can be applied for any arithmetic operation including +=, -=,/=,*=,^=,&=, %=, <<=,>>=, and >>>=. This optimization can be particularly significant if the pattern occurs during a loop.


```solidity
file: /contracts/utils/UFragments.sol

182   _gonBalances[msg.sender] = _gonBalances[msg.sender] - _gonValue;

183    _gonBalances[_to] = _gonBalances[_to] + _gonValue;

199    _gonBalances[_to] = _gonBalances[_to] + _gonValue;

227    _allowedFragments[_from][msg.sender] = _allowedFragments[_from][msg.sender] - _value;

284    _allowedFragments[msg.sender][_spender] = _allowedFragments[msg.sender][_spender] + _addedValue;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L182


## [G-12] Use uint256(1)/uint256(2) instead for true and false boolean states

If you don't use boolean for storage you will avoid Gwarmaccess 100 gas. In addition, state changes of boolean from true to false can cost up to ~20000 gas rather than uint256(2) to uint256(1) that would cost significantly less.

```solidity
file: /contracts/governance/GovernorCharlie.sol

59     mapping(bytes32 => bool) public queuedTransactions;

300    queuedTransactions[_txHash] = true;

342    queuedTransactions[_txHash] = false;

406    queuedTransactions[_txHash] = false;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L59


## [G-13] Using Bools For Storage Incurs Overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
file: /contracts/governance/GovernorCharlie.sol

59     mapping(bytes32 => bool) public queuedTransactions;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L59

```solidity
file: /contracts/periphery/oracles/UniswapV3OracleRelay.sol

17  bool public immutable QUOTE_TOKEN_IS_TOKEN0;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L17

## [G-14] bytes.concat() can be used in place of abi.encodePacked

Given concatenation is not going to be used for hashing bytes.concat is the preferred method to use as its more gas efficient.

```solidity
file: /contracts/governance/GovernorCharlie.sol

/// @audit the  ' _callData ' is bytes on line 344

347    else _callData = abi.encodePacked(bytes4(keccak256(bytes(_signature))), _data);

512    bytes32 _digest = keccak256(abi.encodePacked('\x19\x01', _domainSeparator, _structHash));

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L347

```solidity
file: /contracts/utils/UFragments.sol

328    bytes32 _digest = keccak256(abi.encodePacked('\x19\x01', DOMAIN_SEPARATOR(), _permitDataDigest));

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L328


## [G-15] Use assembly for math (add, sub, mul, div)

Use assembly for math instead of Solidity. You can check for overflow/underflow in assembly to ensure safety. If using Solidity versions < 0.8.0 and you are using Safemath, you can gain significant gas savings by using assembly to calculate values and checking for overflow/underflow.

```solidity
file: /contracts/core/VaultController.sol

357    tokensRegistered = tokensRegistered + 1;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L357

```solidity
file: /contracts/core/VaultController.sol

1040    _priceWithDecimals = _price * 10 ** (18 - _decimals);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L1040

```solidity
file: /contracts/utils/UFragments.sol

336    _nonces[_owner] = _ownerNonce + 1;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L336

```solidity
file: /contracts/utils/ThreeLines0_100.sol

55      int256 _rise = R1 - R0;

61      int256 _rise = R2 - R1;

62      int256 _run = S2 - S1;

86    _result = (_mE6 * _distance) / 1e6 + _b;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol#L55

```solidity
file: /contracts/periphery/oracles/StableCurveLpOracle.sol

53    _value = _lpPrice / 1e18;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L53

## [G-16]  Refactor event to avoid emitting empty data

```solidity
file: /contracts/governance/GovernorCharlie.sol

487    emit VoteCastIndexed(msg.sender, _proposalId, _support, _numberOfVotes, '');

488    emit VoteCast(msg.sender, _proposalId, _support, _numberOfVotes, '');

520    emit VoteCastIndexed(_signatory, _proposalId, _support, _numberOfVotes, '');

521    emit VoteCast(_signatory, _proposalId, _support, _numberOfVotes, '');

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L487