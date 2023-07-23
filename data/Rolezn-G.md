## GAS Summary<a name="GAS Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#gas1-counting-down-in-for-statements-is-more-gas-efficient) | Counting down in `for` statements is more gas efficient | 5 | 1285 |
| [GAS&#x2011;2](#gas2-multiple-accesses-of-a-mappingarray-should-use-a-local-variable-cache) | Multiple accesses of a mapping/array should use a local variable cache | 43 | 3440 |
| [GAS&#x2011;3](#gas3-the-result-of-a-function-call-should-be-cached-rather-than-recalling-the-function) | The result of a function call should be cached rather than re-calling the function | 33 | 1650 |
| [GAS&#x2011;4](#gas4-use-v492-openzeppelin-contracts) | Use v4.9.2 OpenZeppelin contracts | 1 | 28 |
| [GAS&#x2011;5](#gas5-use-nested-if-and-avoid-multiple-check-combinations) | Use nested `if` and avoid multiple check combinations | 5 | 30 |
| [GAS&#x2011;6](#gas6-using-xor-^-and-and-&-bitwise-equivalents) | Using XOR (^) and AND (&) bitwise equivalents | 46 | 598 |

Total: 187 contexts over 6 issues

## Gas Optimizations




### <a href="#gas-summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> Counting down in `for` statements is more gas efficient

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: VaultController.sol

868: for (uint192 _i; _i < enabledTokens.length; ++_i) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L868

```solidity
File: VaultController.sol

896: for (uint192 _i; _i < enabledTokens.length; ++_i) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L896

```solidity
File: GovernorCharlie.sol

259: for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L259

```solidity
File: GovernorCharlie.sol

313: for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L313

```solidity
File: GovernorCharlie.sol

382: for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L382



</details>


#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.AddNum();
            c1.AddNum();
        }
    }
    
    contract Contract0 {
        uint256 num = 3;
        function AddNum() public {
            uint256 _num = num;
            for(uint i=0;i<=9;i++){
                _num = _num +1;
            }
            num = _num;
        }
    }
    
    contract Contract1 {
        uint256 num = 3;
        function AddNum() public {
            uint256 _num = num;
            for(uint i=9;i>=0;i--){
                _num = _num +1;
            }
            num = _num;
        }
    }

#### Gas Test Report

| Contract0 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 77011                                     | 311             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| AddNum                                    | 7040            | 7040 | 7040   | 7040 | 1       |


| Contract1 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 73811                                     | 295             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| AddNum                                    | 3819            | 3819 | 3819   | 3819 | 1       |



### <a href="#gas-summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time. Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. As an example, instead of repeatedly calling `someMap[someIndex]`, save its reference like this: `SomeStruct storage someStruct = someMap[someIndex]` and use it.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: Vault.sol

97: if (isTokenStaked[_token]) {
102: isTokenStaked[_token] = true;
103: _depositAndStakeOnConvex(_token, _booster, balances[_token] + _amount, _poolId);
106: balances[_token] += _amount;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L97

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L102

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L103

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L106



```solidity
File: Vault.sol

142: if (balances[_tokenAddress] == 0) revert Vault_TokenZeroBalance();
143: if (isTokenStaked[_tokenAddress]) revert Vault_TokenAlreadyStaked();
145: isTokenStaked[_tokenAddress] = true;
148: _depositAndStakeOnConvex(_tokenAddress, _booster, balances[_tokenAddress], _poolId);
150: emit Staked(_tokenAddress, balances[_tokenAddress]);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L142

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L143

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L145

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L148

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L150



```solidity
File: Vault.sol

250: _rewards[0] = Reward(CRV, _crvReward);
251: _rewards[1] = Reward(CVX, _cvxReward);
258: _rewards[_i + 2] = Reward(_rewardToken, _earnedReward);
272: _rewards[_i + 2] = Reward(_amphClaimer.AMPH(), _claimableAmph);
275: _rewards[0].amount = _crvReward - _takenCRV;
276: if (_cvxReward > 0) _rewards[1].amount = _cvxReward - _takenCVX;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L250

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L251

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L258

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L272

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L275

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L276



```solidity
File: VaultController.sol

256: _tokenId = _oldVaultController.tokenId(_tokenAddresses[_i]);
260: CollateralInfo memory _collateral = _oldVaultController.tokenCollateralInfo(_tokenAddresses[_i]);
264: enabledTokens.push(_tokenAddresses[_i]);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L256

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L260

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L264



```solidity
File: VaultController.sol

627: if (tokenAddressCollateralInfo[_assetAddress].tokenId == 0) revert VaultController_TokenNotRegistered();
654: if (tokenAddressCollateralInfo[_assetAddress].tokenId == 0) revert VaultController_TokenNotRegistered();
676: CollateralInfo memory _assetInfo = tokenAddressCollateralInfo[_assetAddress];

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L627

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L654

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L676



```solidity
File: VaultController.sol

1016: CollateralInfo memory _collateral = tokenAddressCollateralInfo[_token];
1020: tokenAddressCollateralInfo[_token].totalDeposited =

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L1016

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L1020



```solidity
File: GovernorCharlie.sol

338: if (!queuedTransactions[_txHash]) revert GovernorCharlie_ProposalNotQueued();
342: queuedTransactions[_txHash] = false;
406: queuedTransactions[_txHash] = false;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L338

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L342

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L406



```solidity
File: CurveMaster.sol

23: if (curves[_tokenAddress] == address(0)) revert CurveMaster_TokenNotEnabled();
24: ICurveSlave _curve = ICurveSlave(curves[_tokenAddress]);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/CurveMaster.sol#L23-L24

```solidity
File: CurveMaster.sol

43: address _oldCurve = curves[_tokenAddress];
53: address _oldCurve = curves[_tokenAddress];
44: curves[_tokenAddress] = _curveAddress;
54: curves[_tokenAddress] = _curveAddress;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/CurveMaster.sol#L43

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/CurveMaster.sol#L53

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/CurveMaster.sol#L44

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/CurveMaster.sol#L54



```solidity
File: UFragments.sol

298: uint256 _oldValue = _allowedFragments[msg.sender][_spender];
299: _allowedFragments[msg.sender][_spender] = (_subtractedValue >= _oldValue) ? 0 : _oldValue - _subtractedValue;
286: emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);
301: emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L298-L301

```solidity
File: UFragments.sol

326: uint256 _ownerNonce = _nonces[_owner];
336: _nonces[_owner] = _ownerNonce + 1;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L326

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L336



```solidity
File: ICurvePool.sol

18: uint256[2] memory _minAmounts
34: uint256[2] memory _minAmounts
19: ) external returns (uint256[2] memory _amounts);
21: uint256[2] memory _amounts,
30: uint256[2] memory _amounts,

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L18

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L34

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L19

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L21

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L30





</details>



### <a href="#gas-summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> The result of a function call should be cached rather than re-calling the function

External calls are expensive. Results of external function calls should be cached rather than call them multiple times. Consider caching the following:

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: AMPHClaimer.sol

90: CVX.safeTransferFrom(msg.sender, owner(), _cvxAmountToSend);
91: CRV.safeTransferFrom(msg.sender, owner(), _crvAmountToSend);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L90-L91

```solidity
File: AMPHClaimer.sol

129: IERC20(_token).transfer(owner(), _amount);
131: emit RecoveredDust(_token, owner(), _amount);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L129

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L131



```solidity
File: USDA.sol

131: uint256 _balance = this.balanceOf(_msgSender());
141: uint256 _balance = this.balanceOf(_msgSender());
133: _withdraw(_susdWithdrawn, _msgSender());

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L131-L133

```solidity
File: USDA.sol

150: if (_susdAmount > this.balanceOf(_msgSender())) revert USDA_InsufficientFunds();
154: _burn(_msgSender(), _susdAmount);
184: _burn(_msgSender(), _susdAmount);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L150

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L154

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L184



```solidity
File: Vault.sol

194: _rewardToken.transfer(_msgSender(), _earnedReward);
210: _amphClaimer.claimable(address(this), this.id(), _totalCvxReward, _totalCrvReward);
216: _amphClaimer.claimAmph(this.id(), _totalCvxReward, _totalCrvReward, _msgSender());
216: _amphClaimer.claimAmph(this.id(), _totalCvxReward, _totalCrvReward, _msgSender());
223: if (_totalCvxReward > 0) CVX.transfer(_msgSender(), _totalCvxReward);
224: if (_totalCrvReward > 0) CRV.transfer(_msgSender(), _totalCrvReward);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L194

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L210

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L216

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L216

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L223

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L224



```solidity
File: VaultController.sol

282: _vaultAddress = _createVault(vaultsMinted, _msgSender());
287: walletVaultIDs[_msgSender()].push(vaultsMinted);
290: emit NewVault(_vaultAddress, vaultsMinted, _msgSender());

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L282

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L287

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L290



```solidity
File: VaultController.sol

592: _baseAmount = _safeu192(_vault.baseLiability());
603: if (_baseAmount > _vault.baseLiability()) revert VaultController_RepayTooMuch();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L592

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L603



```solidity
File: VaultController.sol

673: usda.vaultControllerBurn(_msgSender(), _usdaToRepurchase);
684: _vault.controllerTransfer(_assetAddress, _msgSender(), _tokensToLiquidate - _liquidationFee);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L673

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L684



```solidity
File: WUSDA.sol

61: _deposit(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
121: _deposit(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L61

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L121


```solidity
File: WUSDA.sol

81: _withdraw(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
101: _withdraw(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
141: _withdraw(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
161: _withdraw(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L81

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L101

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L141

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L161



```solidity
File: GovernorCharlie.sol

339: if (_getBlockTimestamp() < _eta) revert GovernorCharlie_TimelockNotReached();
340: if (_getBlockTimestamp() > _eta + GRACE_PERIOD) revert GovernorCharlie_TransactionStale();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L339-L340

```solidity
File: StableCurveLpOracle.sol

43: uint256 _minStable = anchoredUnderlyingTokens[0].peekValue();
45: _minStable = Math.min(_minStable, anchoredUnderlyingTokens[_i].peekValue());

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L43

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L45





</details>




### <a href="#gas-summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Use v4.9.2 OpenZeppelin contracts

The v4.9.2 version of OpenZeppelin provides many small gas optimizations.
https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.9.2
#### <ins>Proof Of Concept</ins>

```solidity
File: package.json

    "@openzeppelin/contracts-upgradeable": "4.8.1"
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/../package.json#L48



#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json and require at least version of 4.9.2




### <a href="#gas-summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Use nested `if` and avoid multiple check combinations

Using nested `if`, is cheaper than using `&&` multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

#### <ins>Proof Of Concept</ins>

```solidity
File: AMPHClaimer.sol

88: if (_crvAmountToSend != 0 && _claimedAmph != 0) distributedAmph += (_claimedAmph / 1e12);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L88

```solidity
File: Vault.sol

158: if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L158

```solidity
File: VaultController.sol

1018: if (_increase && (_collateral.totalDeposited + _amount) > _collateral.cap) revert VaultController_CapReached();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L1018

```solidity
File: GovernorCharlie.sol

170: if (amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender)) {
208: if (_emergency && !isWhitelisted(msg.sender)) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L170

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L208






#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
    
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
    
        function testGas() public {
            c0.checkAge(19);
            c1.checkAgeOptimized(19);
        }
    }
    
    contract Contract0 {
    
        function checkAge(uint8 _age) public returns(string memory){
            if(_age>18 && _age<22){
                return "Eligible";
            }
        }
    
    }
    
    contract Contract1 {
    
        function checkAgeOptimized(uint8 _age) public returns(string memory){
            if(_age>18){
                if(_age<22){
                    return "Eligible";
                }
            }
        }
    }

#### Gas Test Report

| Contract0 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 76923                                     | 416             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| checkAge                                  | 651             | 651 | 651    | 651 | 1       |


| Contract1 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 76323                                     | 413             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| checkAgeOptimized                         | 645             | 645 | 645    | 645 | 1       |




### <a href="#gas-summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Using XOR (^) and AND (&) bitwise equivalents

Given 4 variables a, b, c and d represented as such:

    0 0 0 0 0 1 1 0 <- a
    0 1 1 0 0 1 1 0 <- b
    0 0 0 0 0 0 0 0 <- c
    1 1 1 1 1 1 1 1 <- d

To have a == b means that every 0 and 1 match on both variables. Meaning that a XOR (operator ^) would evaluate to 0 ((a ^ b) == 0), as it excludes by definition any equalities.Now, if a != b, this means that there’s at least somewhere a 1 and a 0 not matching between a and b, making (a ^ b) != 0.Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas.However, it is much cheaper to use the bitwise OR operator (|) than comparing the truthy or falsy values.These are logically equivalent too, as the OR bitwise operator (|) would result in a 1 somewhere if any value is not 0 between the XOR (^) statements, meaning if any XOR (^) statement verifies that its arguments are different.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: AMPHClaimer.sol

152: if (_total == 0) return 0;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L152

```solidity
File: AMPHClaimer.sol

169: if (_crvTotalRewards == 0 || _amphBalance == 0) return (0, 0, 0);
180: if (_amphByCrv == 0) return (0, 0, 0);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L169

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L180



```solidity
File: AMPHClaimer.sol

203: if (_tokenAmountToSend == 0) return 0;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L203

```solidity
File: USDA.sol

102: if (_susdAmount == 0) revert USDA_ZeroAmount();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L102

```solidity
File: USDA.sol

148: if (reserveAmount == 0) revert USDA_EmptyReserve();
149: if (_susdAmount == 0) revert USDA_ZeroAmount();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L148-L149

```solidity
File: USDA.sol

162: if (_susdAmount == 0) revert USDA_ZeroAmount();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L162

```solidity
File: USDA.sol

183: if (_susdAmount == 0) revert USDA_ZeroAmount();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L183

```solidity
File: USDA.sol

203: if (_susdAmount == 0) revert USDA_ZeroAmount();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L203

```solidity
File: Vault.sol

91: if (_amount == 0) revert Vault_AmountZero();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L91

```solidity
File: Vault.sol

141: if (_poolId == 0) revert Vault_TokenCanNotBeStaked();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L141

```solidity
File: Vault.sol

171: if (_collateralInfo.tokenId == 0) revert Vault_TokenNotRegistered();
206: if (_totalCrvReward > 0 || _totalCvxReward > 0) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L171

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L206



```solidity
File: VaultController.sol

257: if (_tokenId == 0) revert VaultController_WrongCollateralAddress();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L257

```solidity
File: VaultController.sol

392: if (_collateral.tokenId == 0) revert VaultController_TokenNotRegistered();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L392

```solidity
File: VaultController.sol

625: if (_tokensToLiquidate == 0) revert VaultController_LiquidateZeroTokens();
627: if (tokenAddressCollateralInfo[_assetAddress].tokenId == 0) revert VaultController_TokenNotRegistered();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L625

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L627



```solidity
File: VaultController.sol

652: if (_tokensToLiquidate == 0) revert VaultController_LiquidateZeroTokens();
654: if (tokenAddressCollateralInfo[_assetAddress].tokenId == 0) revert VaultController_TokenNotRegistered();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L652

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L654



```solidity
File: VaultController.sol

808: if (_vaultAddress == address(0)) revert VaultController_VaultDoesNotExist();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L808

```solidity
File: VaultController.sol

848: if (_vaultAddress == address(0)) revert VaultController_VaultDoesNotExist();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L848

```solidity
File: VaultController.sol

871: if (_collateral.ltv == 0) continue;
877: if (_balance == 0) continue;
880: if (_rawPrice == 0) continue;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L871

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L877

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L880



```solidity
File: VaultController.sol

899: if (_collateral.ltv == 0) continue;
905: if (_balance == 0) continue;
908: if (_rawPrice == 0) continue;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L899

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L905

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L908



```solidity
File: VaultController.sol

933: if (_timeDifference == 0) return 0;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L933

```solidity
File: VaultController.sol

1017: if (_collateral.tokenId == 0) revert VaultController_TokenNotRegistered();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L1017

```solidity
File: GovernorCharlie.sol

168: if (quorumVotes == 0) revert GovernorCharlie_NotActive();
174: _targets.length != _values.length || _targets.length != _signatures.length || _targets.length != _calldatas.length
176: if (_targets.length == 0) revert GovernorCharlie_NoActions();
182: if (_proposersLatestProposalState == ProposalState.Active) revert GovernorCharlie_MultipleActiveProposals();
183: if (_proposersLatestProposalState == ProposalState.Pending) revert GovernorCharlie_MultiplePendingProposals();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L168

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L174

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L176

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L182

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L183



```solidity
File: GovernorCharlie.sol

346: if (bytes(_signature).length == 0) _callData = _data;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L346

```solidity
File: GovernorCharlie.sol

463: if (proposalCount < _proposalId || _proposalId <= initialProposalId) revert GovernorCharlie_InvalidProposalId();
474: else if (_proposal.eta == 0) return ProposalState.Succeeded;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L463

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L474



```solidity
File: GovernorCharlie.sol

518: if (_signatory == address(0)) revert GovernorCharlie_InvalidSignature();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L518

```solidity
File: GovernorCharlie.sol

543: if (_support == 0) _proposal.againstVotes = _proposal.againstVotes + _votes;
544: else if (_support == 1) _proposal.forVotes = _proposal.forVotes + _votes;
545: else if (_support == 2) _proposal.abstainVotes = _proposal.abstainVotes + _votes;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L543-L545

```solidity
File: CurveMaster.sol

26: if (_value == 0) revert CurveMaster_ZeroResult();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/CurveMaster.sol#L26

```solidity
File: ChainlinkOracleRelay.sol

66: if (_stalePriceDelay == 0) revert ChainlinkOracle_ZeroAmount();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L66

```solidity
File: UFragments.sol

333: require(_owner == ecrecover(_digest, _v, _r, _s));
334: if (_owner == address(0x0)) revert UFragments_InvalidSignature();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L333-L334



</details>


#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized(1,2);
            c1.optimized(1,2);
        }
    }
    
    contract Contract0 {
        function not_optimized(uint8 a,uint8 b) public returns(bool){
            return ((a==1) || (b==1));
        }
    }
    
    contract Contract1 {
        function optimized(uint8 a,uint8 b) public returns(bool){
            return ((a ^ 1) & (b ^ 1)) == 0;
        }
    }

#### Gas Test Report

| Contract0 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 46099                                     | 261             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| not_optimized                             | 456             | 456 | 456    | 456 | 1       |


| Contract1 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 42493                                     | 243             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| optimized                                 | 430             | 430 | 430    | 430 | 1       |

