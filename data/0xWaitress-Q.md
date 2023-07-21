
### [N-1] changeCvxRewardFee in AMPHClaimer does not have a cap. 

cvxRewardFee if mis-configured to beyond 100% would break many calculation like claimable due to overflow. Same with changeCrvRewardFee.

```solidity
  function changeCvxRewardFee(uint256 _newFee) external override onlyOwner {
    cvxRewardFee = _newFee;

    emit ChangedCvxRewardFee(_newFee);
  }
```


