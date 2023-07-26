
## Non Critical Issues

| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | The `USDA_ZeroAmount` check should be moved to the `_mint` function from the current location in the `mint` function | 1 |
| [NC-2](#NC-2) | The `USDA_ZeroAmount` check should be moved to the `_burn` function from the current location in the `burn` function | 1 |


### <a name="NC-1"></a>[NC-1] The `USDA_ZeroAmount` check should be moved to the `_mint` function from the current location in the `mint` function

There's other functions to use _mint function, so zeroAmount check should be in the `_mint`

*Instances (1)*:
```diff
File: https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol:L161-L164

    function mint(uint256 _susdAmount) external override paysInterest onlyOwner {
-        if (_susdAmount == 0) revert USDA_ZeroAmount(); // @audit here
        _mint(_msgSender(), _susdAmount);
    }

File: https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol:L167-L178
    function _mint(address _target, uint256 _amount) internal {
+        if (_amount == 0) revert USDA_ZeroAmount();
        uint256 __gonsPerFragment = _gonsPerFragment;
        // the gonbalances of the sender is in gons, therefore we must multiply the deposit amount, which is in fragments, by gonsperfragment
        _gonBalances[_target] += _amount * __gonsPerFragment;
        // total supply is in fragments, and so we add amount
        _totalSupply += _amount;
        // and totalgons of course is in gons, and so we multiply amount by gonsperfragment to get the amount of gons we must add to totalGons
        _totalGons += _amount * __gonsPerFragment;
        // emit both a mint and transfer event
        emit Transfer(address(0), _target, _amount);
        emit Mint(_target, _amount);
    }

```

### <a name="NC-2"></a>[NC-2] The `USDA_ZeroAmount` check should be moved to the `_burn` function from the current location in the `burn` function

There's other functions to use _burn function, so zeroAmount check should be in the `_burn`

*Instances (1)*:
```diff
File: https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol:L182-L185

    function burn(uint256 _susdAmount) external override paysInterest onlyOwner {
-        if (_susdAmount == 0) revert USDA_ZeroAmount(); // @audit move to _burn function
        _burn(_msgSender(), _susdAmount);
    }

File: https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol:L188-L198
    function _burn(address _target, uint256 _amount) internal {
+        if (_amount == 0) revert USDA_ZeroAmount();
        uint256 __gonsPerFragment = _gonsPerFragment;
        // modify the gonbalances of the sender, subtracting the amount of gons, therefore amount * gonsperfragment
        _gonBalances[_target] -= (_amount * __gonsPerFragment);
        // modify totalSupply and totalGons
        _totalSupply -= _amount;
        _totalGons -= (_amount * __gonsPerFragment);
        // emit both a burn and transfer event
        emit Transfer(_target, address(0), _amount);
        emit Burn(_target, _amount);
    }

```