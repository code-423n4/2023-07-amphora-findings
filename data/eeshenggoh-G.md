## Summary

These are the findings for Gas Optimization. Will start from this hierarchy: 
1) [contracts/core]
2) [contracts/governance]
3) [contracts/periphery]
4) [contracts/utils]

## Vulnerability Details
### [G002] Cache Array Length Outside of Loop
```
File: USDA.sol

47:   modifier paysInterest() {
48:     for (uint256 _i; _i < _vaultControllers.length();) {
49:       IVaultController(_vaultControllers.at(_i)).calculateInterest();
50:       unchecked {
51:         _i++;
52:       }
53:     }
54:     _;
55:   }

```
```
File: Vault.sol
169:     for (uint256 _i; _i < _tokenAddresses.length;) {
170:       IVaultController.CollateralInfo memory _collateralInfo = CONTROLLER.tokenCollateralInfo(_tokenAddresses[_i]);
171:       if (_collateralInfo.tokenId == 0) revert Vault_TokenNotRegistered();
172:       if (_collateralInfo.collateralType != IVaultController.CollateralType.CurveLPStakedOnConvex) {
173:         revert Vault_TokenNotCurveLP();
174:       }

```
```
File: Vault.sol
169:     for (uint256 _i; _i < _tokenAddresses.length;) {
170:       IVaultController.CollateralInfo memory _collateralInfo = CONTROLLER.tokenCollateralInfo(_tokenAddresses[_i]);
171:       if (_collateralInfo.tokenId == 0) revert Vault_TokenNotRegistered();
172:       if (_collateralInfo.collateralType != IVaultController.CollateralType.CurveLPStakedOnConvex) {
173:         revert Vault_TokenNotCurveLP();
174:       }
```
```
File: VaultController.sol
255:     for (uint256 _i; _i < _tokenAddresses.length;) {
256:       _tokenId = _oldVaultController.tokenId(_tokenAddresses[_i]);
257:       if (_tokenId == 0) revert VaultController_WrongCollateralAddress();
258:       _tokensRegistered++;
```
```
File: VaultController.sol
868:     for (uint192 _i; _i < enabledTokens.length; ++_i) {
869:       CollateralInfo memory _collateral = tokenAddressCollateralInfo[enabledTokens[_i]];
```
```
File: VaultController.sol
896:     for (uint192 _i; _i < enabledTokens.length; ++_i) {
897:       CollateralInfo memory _collateral = tokenAddressCollateralInfo[enabledTokens[_i]];
```
```
File: VaultController.sol
996:       uint256[] memory _tokenBalances = new uint256[](enabledTokens.length);
```
```
File: VaultController.sol
0998:       for (uint256 _j; _j < enabledTokens.length;) {
0999:         _tokenBalances[_j] = _vault.balances(enabledTokens[_j]);
1000: 
1001:         unchecked {
1002:           ++_j;
1003:         }
1004:       }
```

## [contracts/governance/]
```
File: GovernorCharlie.sol
173:     if (
174:       _targets.length != _values.length || _targets.length != _signatures.length || _targets.length != _calldatas.length
175:     ) revert GovernorCharlie_ArityMismatch();
```
```
File: GovernorCharlie.sol
176:     if (_targets.length == 0) revert GovernorCharlie_NoActions();
```
```
File: GovernorCharlie.sol
177:     if (_targets.length > PROPOSAL_MAX_OPERATIONS) revert GovernorCharlie_TooManyActions();
```
```
File: GovernorCharlie.sol
259:     for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {
```
```
File: GovernorCharlie.sol
313:     for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {
```
```
File: GovernorCharlie.sol
346:     if (bytes(_signature).length == 0) _callData = _data;
```
```
File: GovernorCharlie.sol
382:     for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {
```
```
File: StableCurveLpOracle.sol
20:     if (_anchoredUnderlyingTokens.length < 2) revert StableCurveLpOracle_TooFewAnchoredOracles();
```
```
File: StableCurveLpOracle.sol
22:     for (uint256 _i; _i < _anchoredUnderlyingTokens.length;) {
```
```
File: StableCurveLpOracle.sol
44:     for (uint256 _i = 1; _i < anchoredUnderlyingTokens.length;) {
```
## [G002] Impact
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.
In this case where let's take line 998, VaultController as an example, the context remains for others.

Iterates over a loop by using _j as an index. It performs the following operations:

Reading the length property of the enabledTokens array in every iteration which involves a state-read operation with the EVM.
Calling the function of the balance on the _vault, which could potentially be a state-changing operation or an external function call, both of which require a significant amount of gas.
Writing the output of that function call to the _tokenBalances array, a potentially state-changing operation that uses more gas.
For the first point, in Ethereum, all data is stored in a data area called storage, which is persistent between function calls and transactions. Reading data from this area is expensive in terms of gas. If the length of the enabledTokens array is being fetched from storage directly in every iteration, it will invoke a SLOAD operation with the EVM, which is costly.

In this optimized version of the example, we avoid the costly SLOAD operation in every loop iteration and replace it with a MLOAD, which is only fractionally as expensive. That being said, we reduced the gas cost within the loop, which will result in substantial savings when the array length is substantial.

## Proof of Concept
```
Higher Gas Usage
for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
```
```
Lower Gas Usage
uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}
```
## Vulnerability Details
### [G003] Use != 0 instead of > 0 for Unsigned Integer Comparison
```
File: AMPHClaimer.sol
210:     while (_tempAmountReceived > 0) {
```
```
File: Vault.sol
206:     if (_totalCrvReward > 0 || _totalCvxReward > 0) {
```
```
File: Vault.sol
223:       if (_totalCvxReward > 0) CVX.transfer(_msgSender(), _totalCvxReward);
```
```
File: Vault.sol
224:       if (_totalCrvReward > 0) CRV.transfer(_msgSender(), _totalCrvReward);
```
```
File: Vault.sol
276:     if (_cvxReward > 0) _rewards[1].amount = _cvxReward - _takenCVX;
```
```
File: VaultController.sol
558:     if (_fee > 0) usda.vaultControllerMint(owner(), _fee);
```
```
File: VaultController.sol
660:     if (_tokenAmount > 0) _tokensToLiquidate = _tokenAmount;
```
```
File: AnchoredViewRelay.sol
77:     require(_mainValue > 0, 'invalid oracle value');
```
```
File: AnchoredViewRelay.sol
80:     require(_anchorPrice > 0, 'invalid anchor value');
```
```
File: ExponentialNoError.sol
206:     require(_b > 0, _errorMessage);
```
```
File: UFragments.sol
330:     if (uint256(_s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) {
```
## [G003] Impact
When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.
In this case where let's take line 660, VaultController as an example, the context remains for others. `_tokenAmount` cannot be a negative value (as it's an unsigned integer), the conditions `_tokenAmount > 0` and `_tokenAmount != 0` would behave the same. Both would check if `_tokenAmount` is non-zero. Changing the condition will not affect the behavior of the contract in this case. However, if `_tokenAmount` could be negative, the condition `_tokenAmount != 0` would trigger even if `_tokenAmount ` is less than 0, while `_tokenAmount > 0` would only trigger if `_tokenAmount ` is greater than 0.

## [G003] Proof of Concept
```
Higher Gas Usage
// `a` being of type unsigned integer
require(a > 0, "!a > 0");
```
```
Lower Gas Usage
// `a` being of type unsigned integer
require(a != 0, "!a > 0");
```

-----------
## Vulnerability Details
### [G008] Use Shift Right/Left instead of Division/Multiplication if possible

```
File: TriCrypto2Oracle.sol
76:   //     # ((A/A0) * (gamma/gamma0)**2) ** (1/3)
```
```
File: ExponentialNoError.sol
14:   uint256 public constant HALF_EXP_SCALE = EXP_SCALE / 2;
```
```
File: ExponentialNoError.sol
98:     require(_n < 2 ** 224, _errorMessage);
```


## [G008] Impact
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

In this case where let's take line 14, ExponentialNoError as an example, the context remains for others. 
Here, EXP_SCALE >> 1 is equivalent to EXP_SCALE / 2. It computes the value of EXP_SCALE shifted right by one bit, effectively cutting it in half.

## [G008] Proof of Concept
```
Higher Gas Usage
uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
```
```
Lower Gas Usage
uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;
```


As stated in Proof of Concepts
