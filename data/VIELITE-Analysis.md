There are no checks for address(0) in the `_burn()` function in the USDA.sol contract which can lead to unnecessary reverts 

```solidity
function _burn(address _target, uint256 _amount) internal {
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

could be found at `core/solidity/contracts/core/USDA.sol`

### Time spent:
26 hours