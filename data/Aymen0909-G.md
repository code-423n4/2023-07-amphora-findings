# Gas Optimizations

## Summary

|       | Issue        | Instances    |
| :---: |:-------------|:------------:|
| 1 | State variables should be packed to save storage slots | 3 |
| 2 | Emit events outside the for loops | 3 |
| 3 | Multiple address/IDs mappings can be combined into a single mapping of an address/id to a struct | 2 |


## Findings

### 1- State variables should be packed to save storage slots :

State variables inside contract should be packed if possible to save storage slots and thus will save gas cost.

There are 3 instances of this issue:

* Instance 1 :

```
File: core/AMPHClaimer.sol

45      uint256 public cvxRewardFee;
48      uint256 public crvRewardFee;
```

Both variables represents a fee value that cannot go above 1e18, which means that boths variables cannot overflow uint128, and they should be packed to save gas, **save 1 storage slot** : 

```
/// @dev Percentage of rewards taken in CVX (1e18 == 100%)
uint256 public cvxRewardFee;

/// @dev Percentage of rewards taken in CRV (1e18 == 100%)
uint256 public crvRewardFee;
```

* Instance 2 :

```
File: core/VaultController.sol

67      uint192 public protocolFee;
69      uint192 public initialBorrowingFee;
71      uint192 public liquidationFee;
```

The 3 variables represent fee values that cannot go above 1e18, which means that all those variables cannot overflow uint96, and they should be packed to save gas, **save 2 storage slot** :

```
/// @dev The protocol's fee
uint96 public protocolFee;
/// @dev The initial borrowing fee (1e18 == 100%)
uint96 public initialBorrowingFee;
/// @dev The fee taken from the liquidator profit (1e18 == 100%)
uint96 public liquidationFee;
```

* Instance 3 :

```
File: core/AMPHClaimer.sol

35      uint256 public votingDelay;
38      uint256 public votingPeriod;
```

Both variables represent a time value that cannot realistically overflow uint64, so they should be packed to save gas **save 1 storage slot** : 

```
/// @notice The delay before voting on a proposal may take place, once proposed, in blocks
uint64 public votingDelay;

/// @notice The duration of voting on a proposal, in blocks
uint64 public votingPeriod;
```


### 2- Emit events outside the for loops :

As the GovernorCharlie contract includes both the governance logic and the timelock functionality, you should consider changing the way the functions `_queueTransaction`, `_cancelTransaction` and `executeTransaction` are structured.

if we take for example the working of the function `_queueTransaction` :

* It is called when the `queue` function is called.

* The `queue` function loops over all the actions in a proposal and call `queueTransaction` for each one of them.

* The `_queueTransaction` function implement the queue logic and then emit an event `QueueTransaction` for each queued action.

This structure cost a lot of gas as the event is emitted at each iteration of the loop and this effect is even more amplified since the protocol will normally have many proposals with many actions to manage. 

The same issue is present in the `_cancelTransaction` and `executeTransaction` functions.

Thus in order to save gas on the execution of a single proposal, you should consider adding a `_queueTransactions` function which takes a list of all actions for a given proposal to excute and process them and then at the end emit a final global event `QueueTransactions` for all the actions in a proposal.

This change will require to update the `queue` function and make it send all the actions in a proposal instead of looping over them.

**I will give an example of how this can be implemented for the `_queueTransaction`/`queue` functions but the same can be done for both `_cancelTransaction`/`cancel` and `executeTransaction`/`execute` functions.**

The new `_queueTransactions` function to replace the old one [_queueTransaction](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L289-L303) :

```solidity
function _queueTransactions(
    address[] memory targets,
    uint256[] memory values,
    string[] memory signatures,
    bytes[] memory calldatas
    uint256 eta,
    uint256 _delay
) public returns (bytes32[] memory) {
    if (_eta < (_getBlockTimestamp() + _delay)) revert GovernorCharlie_DelayNotReached();

    uint256 length = targets.length;
    bytes32[] memory hashes = new bytes32[](length);
    for (uint256 i = 0; i < length; i++) {
        bytes32 txHash = keccak256(
            abi.encode(targets[i], values[i], signatures[i], calldatas[i], eta)
        );
        
        if (queuedTransactions[txHash]) revert GovernorCharlie_ProposalAlreadyQueued();
        queuedTransactions[txHash] = true;
        hashes[i] = txHash;
    }

    emit QueueTransactions(hashes, targets, values, signatures, calldatas, eta);
    return hashes;
}
```

The `queue` will become : 

```solidity
function queue(uint256 _proposalId) external override {
    if (state(_proposalId) != ProposalState.Succeeded) revert GovernorCharlie_ProposalNotSucceeded();
    Proposal storage _proposal = proposals[_proposalId];
    uint256 _eta = block.timestamp + _proposal.delay;
    _queueTransactions(
        proposal.targets,
        proposal.values,
        proposal.signatures,
        proposal.calldatas,
        eta,
        _proposal.delay
    );
    _proposal.eta = _eta;
    emit ProposalQueuedIndexed(_proposalId, _eta);
    emit ProposalQueued(_proposalId, _eta);
}

```


### 3- Multiple address/IDs mappings can be combined into a single mapping of an address/id to a struct :

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 2 instances of this issue :

File: Vault.sol [Line 42-46](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L42-L46)
```solidity
83:     mapping(address => uint256) public balances;
88:     mapping(address => bool) public isTokenStaked;
```