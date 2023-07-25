### unchecked arithmetics

in which cases where arithmetic operations can’t overflow (mostly incrementing loop counters after comparing them with array lengths in loops), we can mark then unchecked to save gas 

there were 5 found instances of this optimizations:

```jsx
868:for (uint192 _i; _i < enabledTokens.length; ++_i) 
896:for (uint192 _i; _i < enabledTokens.length; ++_i)
259:for (uint256 _i = 0; _i < _proposal.targets.length; _i++)
313:for (uint256 _i = 0; _i < _proposal.targets.length; _i++)
382:for (uint256 _i = 0; _i < _proposal.targets.length; _i++)
```

[[868](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L868),[896](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L896),[259](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L259),[313](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L313),[382](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L382C1-L382C64)]

it in the last three occurrences, we can use pre-increment instead of post-increment to save even more gas

### struct optimization

in [Proposal struct](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/GovernanceStructs.sol#L4C8-L4C16) we can declare the following variables right after each others

- proposer
- canceled
- executed
- executed

doing so will cause the struct to use one less storage slot in the EVM (saving 20000 gas unit), it will also make future reads/writes to that slot cost less (2100 for a cold read vs 100 for a hot read, and 20000/2900 for a cold write vs 800 (TODO : change this) for a hot write), so this optimization will cause most subsequent writes to he same slot of be considered hot

## indexed events

marking event value-variables (such as int, uint, address ..) as indexed causes them to be stored on the stack instead of memory, thus saving gas

instances:

```jsx
ICurveMaster.sol:

16:event VaultControllerSet(address _oldVaultControllerAddress, address _newVaultControllerAddress);
24:event CurveSet(address _oldCurveAddress, address _token, address _newCurveAddress);
32:event CurveForceSet(address _oldCurveAddress, address _token, address _newCurveAddress);

IAMPHClaimer.sol:
21:event "(
    address indexed _vaultClaimer, uint256 _cvxTotalRewards, uint256 _crvTotalRewards, uint256 _amphAmount
  );
36:event RecoveredDust(address indexed _token, address _receiver, uint256 _amount);
42:event ChangedCvxRewardFee(uint256 _newCvxReward);
48:event ChangedCrvRewardFee(uint256 _newCrvReward);
	
IWUSDA.sol:
18:event Deposit(address indexed _from, address indexed _to, uint256 _usdaAmount, uint256 _wusdaAmount);
27:event Withdraw(address indexed _from, address indexed _to, uint256 _usdaAmount, uint256 _wusdaAmount);

IVault.sol:
20:event Deposit(address _token, uint256 _amount);
27:event Withdraw(address _token, uint256 _amount);
34:event ClaimedReward(address _token, uint256 _amount);
41:event Staked(address _token, uint256 _amount);

UFragments.sol:
39:event LogRebase(uint256 indexed epoch, uint256 totalSupply);
40:event LogMonetaryPolicyUpdated(address monetaryPolicy);

IUSDA.sol:
19:event Deposit(address indexed _from, uint256 _value);
26:event Withdraw(address indexed _from, uint256 _value);
33:event Mint(address _to, uint256 _value);
40:event Burn(address _from, uint256 _value);
48:event Donation(address indexed _from, uint256 _value, uint256 _totalSupply);
55:event RecoveredDust(address indexed _receiver, uint256 _amount);
61:event PauserSet(address indexed _pauser);
68:event VaultControllerTransfer(address _target, uint256 _susdAmount);

IVaultController.sol:
23:event InterestEvent(uint64 _epoch, uint192 _amount, uint256 _curveVal);
29:event NewProtocolFee(uint192 _protocolFee);
40:event RegisteredErc20(
    address _tokenAddress, uint256 _ltv, address _oracleAddress, uint256 _liquidationIncentive, uint256 _cap
  );
52:event UpdateRegisteredErc20(
    address _tokenAddress,
    uint256 _ltv,
    address _oracleAddress,
    uint256 _liquidationIncentive,
    uint256 _cap,
    uint256 _poolId
  );
67:event NewVault(address _vaultAddress, uint256 _vaultId, address _vaultOwner);
73:event RegisterCurveMaster(address _curveMasterAddress);
81:event BorrowUSDA(uint256 _vaultId, address _vaultAddress, uint256 _borrowAmount, uint256 _fee);
89:event RepayUSDA(uint256 _vaultId, address _vaultAddress, uint256 _repayAmount);
99:event Liquidate(
    uint256 _vaultId,
    address _assetAddress,
    uint256 _usdaToRepurchase,
    uint256 _tokensToLiquidate,
    uint256 _liquidationFee
  );
118:event RegisterUSDA(address _usdaContractAddress);
125:event ChangedInitialBorrowingFee(uint192 _oldBorrowingFee, uint192 _newBorrowingFee);
132:event ChangedLiquidationFee(uint192 _oldLiquidationFee, uint192 _newLiquidationFee);
```

### caching variables on the stack

- array.length

mostly array lengths in for loops, instead of reading them using `array.length` every time, one could declare a `uint` variable to the stack, and then set it to the array length.

 the following instances were found:

```jsx
StableCurveLpOracle.sol:
22:for (uint256 _i; _i < _anchoredUnderlyingTokens.length;)
44:for (uint256 _i = 1; _i < anchoredUnderlyingTokens.length;)

Vault.sol:
169:for (uint256 _i; _i < _tokenAddresses.length;)

USDA.sol:
48:for (uint256 _i; _i < _vaultControllers.length();)

VaultController.sol:
255:for (uint256 _i; _i < _tokenAddresses.length;)
868:for (uint192 _i; _i < enabledTokens.length; ++_i)
896:for (uint192 _i; _i < enabledTokens.length; ++_i)

259:for (uint256 _i = 0; _i < _proposal.targets.length; _i++)
313:for (uint256 _i = 0; _i < _proposal.targets.length; _i++)
382:for (uint256 _i = 0; _i < _proposal.targets.length; _i++)

GovernorCharlie.sol:
174: /*_targets.length could be cached here*/ _targets.length != _values.length || _targets.length != _signatures.length || _targets.length != _calldatas.length)
```

- global variables: msg.sender and block.number
    
    reading global variables costs more than reading stack variables, consequently we can cache this address on the stack and read it from there every time we need it, instances found include:
    
    ```jsx
    GovernorCharlie.sol:
    159: _propose function reads msg.sender 7 times and block.number 7 times
    462: state function reads block.number 2 times
    485: castVote functions reads msg.sender 3 times
    498: castVoteWithReason function reads msg.sender 3 times
    ```
    
- addresses
    - in addition to the length in `Vault.sol` , `address(this)` was used 2 times int he outer loop (lines 177, and 182), and once in the inner loop (line 191), caching the address in the stack would save `2 + _j` reads in [claimRewards](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/Vault.sol#L164C12-L164C24) function
    - `UFragments.sol`'s constructor uses `address(0x0)` twice, caching on the stack should save deployment cost
- array elements
    - in `VaultController.sol` , some functions reference an array element , then stores it on the stack resulting in 2 memory reads in each iteration, if the function caches the element before accessing it it will avoid 1 memory read each iteration check the following code example code taken from `_getVaultBorrowingPower` function
    
    ```jsx
    function _getVaultBorrowingPower(IVault _vault) private returns (uint192 _borrowPower) {
      
        for (uint192 _i; _i < enabledTokens.length; ++_i) {
          CollateralInfo memory _collateral = tokenAddressCollateralInfo[enabledTokens[_i]];
          // if the ltv is 0, continue
          if (_collateral.ltv == 0) continue;
          // get the address of the token through the array of enabled tokens
          // note that index 0 of enabledTokens corresponds to a vaultId of 1, so we must subtract 1 from i to get the correct index
          address _tokenAddress = enabledTokens[_i];
    			...
      }
    
    instead is should be
    
    function _getVaultBorrowingPower(IVault _vault) private returns (uint192 _borrowPower) {
      
        for (uint192 _i; _i < enabledTokens.length; ++_i) {
    			address _tokenAddress = enabledTokens[_i];
          CollateralInfo memory _collateral = tokenAddressCollateralInfo[_tokenAddress];
          // if the ltv is 0, continue
          if (_collateral.ltv == 0) continue;
          // get the address of the token through the array of enabled tokens
          // note that index 0 of enabledTokens corresponds to a vaultId of 1, so we must subtract 1 from i to get the correct index
          
    			...
      }
    
    since stack reads/writes are very cheap
    ```
    
    - `_migrateCollateralsFrom` reads `_tokenAddresses[_i]`  3 times in each iterations while it should cache it in stack instead to save gas
    
    ```jsx
    function _migrateCollateralsFrom(IVaultController _oldVaultController, address[] memory _tokenAddresses) internal {
        for (uint256 _i; _i < _tokenAddresses.length;) {
          _tokenId = _oldVaultController.tokenId(_tokenAddresses[_i]);
    			...
          CollateralInfo memory _collateral = _oldVaultController.tokenCollateralInfo(_tokenAddresses[_i]);
    			...
          enabledTokens.push(_tokenAddresses[_i]);
          tokenAddressCollateralInfo[_tokenAddresses[_i]] = _collateral;
        }
    
    should look like this instead
    
    function _migrateCollateralsFrom(IVaultController _oldVaultController, address[] memory _tokenAddresses) internal {
    		address _currentTokenAddresses;
        for (uint256 _i; _i < _tokenAddresses.length;) {
    			_currentTokenAddresses = _tokenAddresses[_i];
          _tokenId = _oldVaultController.tokenId(_currentTokenAddresses);
    			...
          CollateralInfo memory _collateral = _oldVaultController.tokenCollateralInfo(_currentTokenAddresses);
    			...
          enabledTokens.push(_currentTokenAddresses);
          tokenAddressCollateralInfo[_tokenAddresses[_i]] = _collateral;
        }
    ```
    
    ### state variables that should be immutable
    
    marking state variables that are only changed int the constructor and nowhere else as `immutable` saves gas
    
    multiple state variables like this were found :
    
    ```jsx
    USDA.sol:
    26: IERC20 public sUSD;
    
    UFragments.sol:
    67: uint256 public _totalGons;
    76: string public name;
    77:string public symbol;
    
    AnchoredViewRelay.sol:
    12:IOracleRelay public anchorRelay;
    15:IOracleRelay public mainRelay;
    18:uint256 public widthNumerator;
    21:uint256 public widthDenominator;
    24:uint256 public staleWidthNumerator;
    27:uint256 public staleWidthDenominator;
    
    CTokenOracle.sol:
    21:ICToken public cToken;
    
    OracleRelay.sol:
    10:OracleType public oracleType;
    ```
    
    ### memory function arguments that should be declared as `calldata` instead
    
    gas can be saved by declaring arguments that are not changed inside the function as `calldata` , few instances of this were found
    
    ```jsx
    Vault.so:
    164: `_tokenAddresses` array in `claimRewards` function
    
    GovernorCharlie.sol:
    120: all arguments in `propose` function
    139: all arguments in `proposeEmergency` function
    ```
    
    ### use of bytes32 instead of string