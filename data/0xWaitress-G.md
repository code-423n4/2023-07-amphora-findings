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