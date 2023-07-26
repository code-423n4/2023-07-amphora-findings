## Unnecessary _eta check in `GovernorCharlie._queueTransaction` 
`if (_eta < (_getBlockTimestamp() + _delay)) revert GovernorCharlie_DelayNotReached();` is not necessary.
https://github.com/code-423n4/2023-07-amphora/blob/5d1cea9410db5448760c834f001af04a72edf3e0/core/solidity/contracts/governance/GovernorCharlie.sol#L297
It is because `_eta = block.timestamp + delay` in [`GovernorCharlie.queue`](https://github.com/code-423n4/2023-07-amphora/blob/5d1cea9410db5448760c834f001af04a72edf3e0/core/solidity/contracts/governance/GovernorCharlie.sol#L258).

## Revert with the original error message in `GovernorCharlie.executeTransaction`.
Openzeppelin TimelockController preserves the original error message. It is more informational than `GovernorCharlie_ProposalNotSucceeded`.
```solidity
function _execute(address target, uint256 value, bytes calldata data) internal virtual {
    (bool success, bytes memory returndata) = target.call{value: value}(data);
    Address.verifyCallResult(success, returndata);
}
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/governance/TimelockController.sol#L412-L415
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol#L204