## Low Issues
### `UniswapV3OracleRelay` should validate token0/1 is actually the quote token
The `UniswapV3OracleRelay` constructor contains the following logic:
```solidity
constructor(uint32 _lookback, address _poolAddress, bool _quoteTokenIsToken0) OracleRelay(OracleType.Uniswap) {
    ...
    QUOTE_TOKEN_IS_TOKEN0 = _quoteTokenIsToken0;
    POOL = IUniswapV3Pool(_poolAddress);

    address _token0 = POOL.token0();
    address _token1 = POOL.token1(); // @audit-info - LOW: should validate that quote token is actually weth

    (BASE_TOKEN, QUOTE_TOKEN) = QUOTE_TOKEN_IS_TOKEN0 ? (_token1, _token0) : (_token0, _token1);
    BASE_TOKEN_DECIMALS = IERC20Metadata(BASE_TOKEN).decimals();
    QUOTE_TOKEN_DECIMALS = IERC20Metadata(QUOTE_TOKEN).decimals();
    ...
}
```
This is inherited by only `UniswapV3TokenOracleRelay` which expects the quote token to be ETH, corresponding to the ETH/USDC data feed address, which will be wrapped as WETH on Uniswap. Currently, as above, the pool tokens are retrieved directly from the pool address but there is no validation that either token is indeed WETH. A check should be added to ensure the quote token assigned is actually WETH.

### Unchecked return values of `EnumerableSet.AddressSet::add/remove`
```solidity
function addVaultController(address _vaultController) external onlyOwner {
    _vaultControllers.add(_vaultController); // @audit-info - LOW: unchecked return value, only grant/revoke role & emit event if true
    _grantRole(VAULT_CONTROLLER_ROLE, _vaultController);

    emit VaultControllerAdded(_vaultController);
  }
```
When calling `add/remove` on `EnumerableSet.AddressSet` it is important to check the boolean return values as if the address in question has already been added/does not exist in the set respectively then these functions will return false. In this case, events will be erroneously emitted when calling `USDA::addVaultController`, `USDA::removeVaultController` and `USDA::removeVaultControllerFromList` which could cause issues for off-chain infrastructure.

### No limit on `AMPHClaimer::changeCvxRewardFee` input value
```solidity
function changeCvxRewardFee(uint256 _newFee) external override onlyOwner {
    cvxRewardFee = _newFee; // @audit-info - LOW: should limit these to BASE otherwise fee can exceed reward

    emit ChangedCvxRewardFee(_newFee);
}
```
Currently, `AMPHClaimer::changeCvxRewardFee` can be called with any value desired by the owner. Unlike other functions, such as `VaultController::changeProtocolFee`, which limit the maximum value for the state variable update, this function does not.
```solidity
function changeProtocolFee(uint192 _newProtocolFee) external override onlyOwner {
    if (_newProtocolFee >= 1e18) revert VaultController_FeeTooLarge();
    protocolFee = _newProtocolFee;
    emit NewProtocolFee(_newProtocolFee);
  }
```
This finding also applies to `AMPHClaimer::changeCrvRewardFee`. To avoid cases where the CVX/CRV fees taken by the protocol exceed the rewards earned, these functions and the constructor should cap the state change to 1e18 (or 100%) as given by the `BASE` constant.