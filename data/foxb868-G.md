## GAS-01 - Large Loop in _calculate Function
In the `_calculate` function, the loop is used to calculate the amount of AMPH to mint based on the input token amount `(_tempAmountReceived)`. The loop iterates to determine how much AMPH should be minted for a given amount of input tokens (CRV) based on certain conditions and cliff limits.

Instance: https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/AMPHClaimer.sol#L210
```solidity
while (_tempAmountReceived > 0) {
  // ... loop body ...
}
```
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/AMPHClaimer.sol#L210-L246

This` while` loop is used to calculate the amount of AMPH to mint based on the input token amount `(_tempAmountReceived)`. The loop performs multiple calculations to determine the correct amount of AMPH to mint based on certain conditions and cliff limits.

The loop can be computationally expensive, especially if `_tempAmountReceived` is a large value. As the loop iterates, the gas cost increases linearly with the number of iterations. If `_tempAmountReceived` is very large, it could lead to significantly higher gas costs, which may exceed the block gas limit. Transactions consuming too much gas may fail or become unreasonably expensive for users.

## GAS-02 - Complex Mathematical Operations in `_calculate` Function.
In this line, `_amphForThisTurn` is calculated based on _rate (a value derived from previous calculations), multiplied by `_tempAmountReceived` (the input token amount), and then divided by 1e12 and 1e6. This involves multiple divisions and multiplications, which can lead to significant gas consumption, especially if `_tempAmountReceived` is a large value.

[#L222](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/AMPHClaimer.sol#L222)
```solidity
_amphForThisTurn = ((_rate * _tempAmountReceived) / 1e12) / 1e6; // 1e6
```
To optimize gas usage in this specific line, consider avoiding floating-point arithmetic and complex divisions/multiplications. One possible approach is to use fixed-point arithmetic or integer-based calculations instead of floating-point values (like 1e12 and 1e6) to perform the necessary calculations. Fixed-point arithmetic can significantly reduce the gas consumption in mathematical operations.

```solidity
uint256 _rateScaled = _rate * 1e12; // Scale _rate by 1e12 to maintain precision
_amphForThisTurn = (_rateScaled * _tempAmountReceived) / 1e6; // Perform the multiplication and then divide by 1e6
```
By employing fixed-point arithmetic in the above manner, you can achieve the same result without the need for multiple divisions, leading to better gas efficiency.




## GAS-03 - Gas Limit Concern in `_migrateCollateralsFrom` Function.
The `_migrateCollateralsFrom` function is responsible for migrating collaterals from an old vault controller `(_oldVaultController)` to the current contract. During this migration, it processes an array of `_tokenAddresses`, performing updates for each collateral. However, if the number of `_tokenAddresses` is substantial, it could lead to high gas consumption, potentially exceeding the block gas limit.

[function _migrateCollateralsFrom](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L252-L274)
```solidity
function _migrateCollateralsFrom(IVaultController _oldVaultController, address[] memory _tokenAddresses) internal {
    // ...
    for (uint256 _i; _i < _tokenAddresses.length;) {
        // Loop body
        // ...
        unchecked {
            ++_i;
        }
    }
    // ...
}
```
The loop iterates over the `_tokenAddresses` array to process each token address. If the array contains a large number of elements, the loop execution could become expensive in terms of gas consumption.


Review the loop logic and consider optimizing it to reduce gas consumption. Look for opportunities to minimize computations and reduce the overall gas cost.


## GAS-04 - In the `_linearInterpolation` function, a division operation is performed to calculate _mE6.
The repetitive computations that could lead to inefficient gas usage occur in the valueAt function. Specifically, the calculations for each segment of the piecewise linear curve are performed sequentially, and if the same _xValue is evaluated multiple times, the same computations will be repeated for each evaluation.

[#ThreeLines0_100.sol](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/ThreeLines0_100.sol#L47-L68)
```solidity
function valueAt(int256 _xValue) external view override returns (int256 _value) {
  // the x value must be between 0 (0%) and 1e18 (100%)
  if (_xValue < 0) revert ThreeLines0_100_InputTooSmall();

  if (_xValue > 1e18) _xValue = 1e18;

  // first piece of the piece wise function
  if (_xValue < S1) {
    int256 _rise = R1 - R0;
    int256 _run = S1;
    return _linearInterpolation(_rise, _run, _xValue, R0);
  }
  // second piece of the piece wise function
  if (_xValue < S2) {
    int256 _rise = R2 - R1;
    int256 _run = S2 - S1;
    return _linearInterpolation(_rise, _run, _xValue - S1, R1);
  }
  // the third and final piece of piecewise function, simply a line
  // since we already know that _xValue <= 1e18, this is safe
  return R2;
}
```
In the `valueAt` function, three different pieces of the piecewise linear curve are evaluated based on the input `_xValue`. For each piece, specific calculations are performed to determine the corresponding output `_value`. If the same `_xValue` is evaluated multiple times within the same transaction or contract execution, these calculations will be redundantly repeated.

In the `_linearInterpolation` function, the slope `_mE6` is calculated using a division operation, `_rise * 1e6 / _run`. This division is performed to scale the slope for better precision and accuracy in the interpolation. However, division operations are relatively expensive in terms of gas consumption, especially in hot paths where the function is called frequently.

[L83](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/ThreeLines0_100.sol#L83)
```solidity
// _rise * 1e6 is divided by _run to calculate the slope _mE6
int256 _mE6 = (_rise * 1e6) / _run;
```

To minimize the use of division operations and improve gas efficiency, developers can consider using fixed-point arithmetic instead. By precomputing the reciprocal of `_run` and using multiplication instead of division, gas consumption can be reduced. Here's an example of how the code can be optimized:
```solidity
// Precompute the reciprocal of _run
int256 _reciprocalRun = 1e6 / _run;

// Use multiplication instead of division to calculate the slope _mE6
int256 _mE6 = _rise * _reciprocalRun;
```
By using fixed-point arithmetic and avoiding division operations, gas efficiency can be significantly improved in the `_linearInterpolation` function.