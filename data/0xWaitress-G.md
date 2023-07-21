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