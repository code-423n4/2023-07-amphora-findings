## [NC-1] Use more descriptive and related variable names instead of `_foo` and `_bar`
Variable names `_foo` and `_bar` are used in the `_getRate` function of the AMPHClaimer.sol contract.
File: https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/AMPHClaimer.sol#L195

```
function _getRate(uint256 _distributedAmph) internal pure returns (uint256 _rate) {
    uint256 _foo = (_TWENTY_FIVE_THOUSANDS * BASE_SUPPLY_PER_CLIFF) / Math.max(_distributedAmph, _FIFTY_MILLIONS);
    uint256 _bar = (_distributedAmph * 1e12) / (BASE_SUPPLY_PER_CLIFF * _FIFTY); //@audit div before mul.
//@audit use meaningful variable name.
    _rate = 1e6 + (_foo - _bar);
  }
```

#### Recommended Mitigation Steps
Use more descriptive variable names other than `_foo` and `_bar`.

## [NC-2] Admin can mint large amount of AmphoraProtocolToken since there is no maximum cap

The mint function has no maximum totalsupply cap, so admin can mint large amount of token.

```
function mint(address _dst, uint256 _rawAmount) public onlyOwner {
    _mint(_dst, _rawAmount);
  }//@audit totalsupply should have a cap.
```
#### Recommended Mitigation Step
implement the max supply cap