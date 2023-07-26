
## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Use the returns variable directly instead of declaring the variables | 3 |

### <a name="GAS-1"></a>[GAS-1] Use the returns variable directly instead of declaring the variables

*Instances (3)*:
```diff
File: https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol

-171:         uint256 _cvxRewardsFeeToExchange = _totalToFraction(_cvxTotalRewards, cvxRewardFee);
+171:         _cvxAmountToSend = _totalToFraction(_cvxTotalRewards, cvxRewardFee);


-172:         uint256 _crvRewardsFeeToExchange = _totalToFraction(_crvTotalRewards, crvRewardFee);
+172:         _crvAmountToSend = _totalToFraction(_crvTotalRewards, crvRewardFee);

-174:         uint256 _amphByCrv = _calculate(_crvRewardsFeeToExchange);
+174:         _claimableAmph = _calculate(_crvAmountToSend);

176:            // Check if all cliffs consumed
177:            if (_getCliff((_claimableAmph / 1e12) + distributedAmph) >= TOTAL_CLIFFS) return (0, 0, 0);
178:
179:            // check for rounding errors
180:            if (_claimableAmph == 0) return (0, 0, 0);
181:
182:            if (_amphBalance >= _claimableAmph) {
-184:      _cvxAmountToSend = _cvxRewardsFeeToExchange;
-185:      _crvAmountToSend = _crvRewardsFeeToExchange;
-186:      _claimableAmph = _amphByCrv;
```