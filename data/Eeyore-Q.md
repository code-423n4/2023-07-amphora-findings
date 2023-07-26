## [L-01] Absence of duplicate address validation in `_tokenAddresses` array in `VaultController#_migrateCollateralsFrom` function

### Description

The `VaultController` contract offers the option to migrate token collateral data from an old contract during the `constructor` call. 

However, if the `_migrateCollateralsFrom` function receives the same valid token address multiple times in the `_tokenAddresses` array, it can misconfigure the new `VaultController` contract.

This situation could lead to an overlong `enabledTokens` array that contains duplicate token addresses. As this array is utilized for collateral calculations, each entry should be unique to ensure accurate computations.

### Recommendation

An additional check should be added to the `_migrateCollateralsFrom` function to prevent duplicate addresses from entering the `enabledTokens` array. 

```diff
    if (_tokenId == 0) revert VaultController_WrongCollateralAddress();
+   if (tokenAddressCollateralInfo[_tokenAddress].tokenId != 0) revert VaultController_TokenAlreadyRegistered();
    _tokensRegistered++;
```

## [L-02] `USDA` contract address should be registered only once in `VaultController` contract

### Description

In the `registerUSDA` function, no check is present that prevents changing an already registered `USDA` address. 

There should never be a need to alter a set `USDA` address, as such a change could disrupt the composability of the `Amphora` protocol and potentially lead to user fund losses.

### Recommendation

Add a one-time registration check to the `registerUSDA` function to ensure that the `USDA` address cannot be modified once it has been set.

```diff
    function registerUSDA(address _usdaAddress) external override onlyOwner {
+   if (usda != address(0)) revert VaultController_UsdaAlreadyRegistered();
    usda = IUSDA(_usdaAddress);
```

## [L-03] Incorrect check in `VaultController#changeInitialBorrowingFee` function

### Description

In the `changeInitialBorrowingFee` function, the conditional check used to verify the new borrowing fee against `MAX_INIT_BORROWING_FEE` is flawed. The condition `_newBorrowingFee >= MAX_INIT_BORROWING_FEE` should be `_newBorrowingFee > MAX_INIT_BORROWING_FEE` to allow `MAX_INIT_BORROWING_FEE` as a valid maximum value.

### Recommendation

Correct the conditional check in the `changeInitialBorrowingFee` function to include `MAX_INIT_BORROWING_FEE` as the maximum value for `_newBorrowingFee`.

```diff
function changeInitialBorrowingFee(uint192 _newBorrowingFee) external override onlyOwner {
-   if (_newBorrowingFee >= MAX_INIT_BORROWING_FEE) revert VaultController_FeeTooLarge();
+   if (_newBorrowingFee > MAX_INIT_BORROWING_FEE) revert VaultController_FeeTooLarge();
```
## [L-04] Incorrect validation in `VaultController#changeLiquidationFee` function

### Description

The `changeLiquidationFee` function includes an incorrect conditional check to validate the new liquidation fee. The current condition `_newLiquidationFee >= 1e18` should instead be `_newLiquidationFee > 1e18`. 

Additionally, the value should be limited to a reasonable amount, as `1e18` represents a 100% fee, which may be excessive.

### Recommendation

Correct the conditional check in the `changeLiquidationFee` function to properly validate the new liquidation fee and limit it to a reasonable amount.

```diff
function changeLiquidationFee(uint192 _newLiquidationFee) external override onlyOwner {
-   if (_newLiquidationFee >= 1e18) revert VaultController_FeeTooLarge();
+   if (_newLiquidationFee > MAX_LIQUIDATION_FEE) revert VaultController_FeeTooLarge();
```

Where `MAX_LIQUIDATION_FEE` should be a constant representing the maximum reasonable liquidation fee value.