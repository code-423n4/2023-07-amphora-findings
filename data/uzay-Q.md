# Some functions revert on failure even though they are expected to return false instead

In the `UFragments.sol` contract, the transfer and approval functions return `true` on success and `revert` on failure. However, according to the natspec comments written by the protocol developers, these functions are expected to return `false` on failure.

## PoC
```javascript
/**
* @notice Transfer tokens to a specified address.
* @param _to The address to transfer to.
* @param _value The amount to be transferred.
* @return _success True on success, false otherwise. @audit function never returns false
*/
function transfer(address _to, uint256 _value) external override validRecipient(_to) returns (bool _success) {
	uint256 _gonValue = _value * _gonsPerFragment;
	
	_gonBalances[msg.sender] = _gonBalances[msg.sender] - _gonValue;
	_gonBalances[_to] = _gonBalances[_to] + _gonValue;
	
	emit  Transfer(msg.sender, _to, _value);
	return  true;
}
```

## Affected functions
`transfer()`
`transferAll()`
`transferFrom()`
`transferAllFrom()`
`approve()`
`increaseAllowance()`
`decreaseAllowance()`

## Recommendation
If the comments in the code were a mistake, you can remove the related comments. However, if the comments accurately state the intended behavior, you should modify the function in a way that it returns false when the execution fails.