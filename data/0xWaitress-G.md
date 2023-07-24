### [G-1] paysInterest in USDA access storage repeatedly in a loop

consider retrieve `_vaultControllers.length()` once and use it from memory in the for loop
```solidity
  modifier paysInterest() {
    for (uint256 _i; _i < _vaultControllers.length();) {
      IVaultController(_vaultControllers.at(_i)).calculateInterest();
      unchecked {
        _i++;
      }
    }
    _;
  }
```

### [G-2] _mint/_burn in USDA repeats computation of `_amount * _gonsPerFragment`.

```solidity
    _gonBalances[_target] += _amount * __gonsPerFragment;
    // total supply is in fragments, and so we add amount
    _totalSupply += _amount;
    // and totalgons of course is in gons, and so we multiply amount by gonsperfragment to get the amount of gons we must add to totalGons
    _totalGons += _amount * __gonsPerFragment;
```

### [G-3] liquidation gas fee increases as enabledToken increases since external read on Vault increases linearly regardless if the liquidatedUser deposited the asset or not.

liquidateVault would call `_getVaultBorrowingPower` at the end of execution to verify that the user is still not over-collateralized. However this function would loop over all the vault's balance on all enabledTokens, each is an external read on Vault, costing linear increasing gas even though the user may have just 1 asset deposited

```solidity
  function _getVaultBorrowingPower(IVault _vault) private returns (uint192 _borrowPower) {
    // loop over each registed token, adding the indivuduals ltv to the total ltv of the vault
    for (uint192 _i; _i < enabledTokens.length; ++_i) {
      CollateralInfo memory _collateral = tokenAddressCollateralInfo[enabledTokens[_i]];
      // if the ltv is 0, continue
      if (_collateral.ltv == 0) continue;
      // get the address of the token through the array of enabled tokens
      // note that index 0 of enabledTokens corresponds to a vaultId of 1, so we must subtract 1 from i to get the correct index
      address _tokenAddress = enabledTokens[_i];
      // the balance is the vault's token balance of the current collateral token in the loop
      uint256 _balance = _vault.balances(_tokenAddress);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L866-L876

Recommendation:
Consider make a vault-based enabledTokens that gets updated whenever the vault deposit/wthidraw, such that the liquidation only checks the enabledTokens for that particular user/vault.