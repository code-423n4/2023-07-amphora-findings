https://github.com/code-423n4/2023-07-amphora/blob/5d1cea9410db5448760c834f001af04a72edf3e0/core/solidity/contracts/core/Vault.sol#L188-L200
```solidity

  for (uint256 _j; _j < _extraRewards;) {
        IVirtualBalanceRewardPool _virtualReward = _rewardsContract.extraRewards(_j);
        IERC20 _rewardToken = _virtualReward.rewardToken();
        uint256 _earnedReward = _virtualReward.earned(address(this));
        if (_earnedReward != 0) {
          _virtualReward.getReward();
          _rewardToken.transfer(_msgSender(), _earnedReward);
          emit ClaimedReward(address(_rewardToken), _earnedReward);
        }
        unchecked {
          ++_j;
        }
      }

```

transfer token in loop, take all amount in one time transfer is better