### [L-1] _withdraw in USDA does not follow the check effect interaction patterns:

it's better to _burn the _susdAmount first before calling sUSD.transfer which is an external call. If sUSD becomes a token that has beforeTokenTransfer then it has the potential to be subject to re-entrency.

```solidity
  function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
    if (reserveAmount == 0) revert USDA_EmptyReserve();
    if (_susdAmount == 0) revert USDA_ZeroAmount();
    if (_susdAmount > this.balanceOf(_msgSender())) revert USDA_InsufficientFunds();
    // Account for the susd withdrawn
    reserveAmount -= _susdAmount;
    sUSD.transfer(_target, _susdAmount);
    _burn(_msgSender(), _susdAmount);

    emit Withdraw(_target, _susdAmount);
  }
```

### [N-1] changeCvxRewardFee in AMPHClaimer does not have a cap. 

cvxRewardFee if mis-configured to beyond 100% would break many calculation like claimable due to overflow. Same with changeCrvRewardFee.

```solidity
  function changeCvxRewardFee(uint256 _newFee) external override onlyOwner {
    cvxRewardFee = _newFee;

    emit ChangedCvxRewardFee(_newFee);
  }
```


