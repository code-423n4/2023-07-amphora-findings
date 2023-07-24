### Use of `memory` instead of `storage` for struct/array state variables

When fetching data from `storage`, using the `memory` keyword to assign it to a variable reads all fields of the struct/array and incurs a Gcoldsload (**2100 gas**) for each field.

This can be avoided by declaring the variable with the `storage` keyword and caching the necessary fields in stack variables.

The `memory` keyword should only be used if the entire struct/array is being returned or passed to a function that requires `memory`, or if it is being read from another `memory` array/struct.

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L170

```solidity
File: [/contracts/core/Vault.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L170) 

170: IVaultController.CollateralInfo memory _collateralInfo = CONTROLLER.tokenCollateralInfo(_tokenAddresses[_i]);
```

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L452

```solidity
File: [/contracts/governance/GovernorCharlie.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L452)

449: function getReceipt(
450:    uint256 _proposalId,
451:    address _voter
452:  ) external view override returns (Receipt memory _votingReceipt) { //@audit
453:    _votingReceipt = proposalReceipts[_proposalId][_voter];
454:  }
```

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L260C7-L260C104

```solidity
File: /contracts/core/VaultController.sol

260: CollateralInfo memory _collateral = _oldVaultController.tokenCollateralInfo(_tokenAddresses[_i]);
```

Use storage value

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L1020C6-L1020C39

```solidity
File: 

1020: tokenAddressCollateralInfo[_token].totalDeposited =
      _increase ? _collateral.totalDeposited + _amount : _collateral.totalDeposited - _amount;
	  } 
```

## **Emitting storage values instead of the memory one**

Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L598

```solidity
File:

587: emit NewDelay(_oldTimelockDelay, proposalTimelockDelay);

598: emit NewEmergencyDelay(_oldEmergencyTimelockDelay, emergencyTimelockDelay);

609: emit VotingDelaySet(_oldVotingDelay, votingDelay);

620: emit VotingPeriodSet(_oldVotingPeriod, votingPeriod);

631: emit EmergencyVotingPeriodSet(_oldEmergencyVotingPeriod, emergencyVotingPeriod);

642: emit ProposalThresholdSet(_oldProposalThreshold, proposalThreshold);

653: emit NewQuorum(_oldQuorumVotes, quorumVotes);

664: emit NewEmergencyQuorum(_oldEmergencyQuorumVotes, emergencyQuorumVotes);

688: emit WhitelistGuardianSet(_oldGuardian, whitelistGuardian);

699: emit WhitelistGuardianSet(_oldGuardian, whitelistGuardian);

710: emit OptimisticQuorumVotesSet(_oldOptimisticQuorumVotes, optimisticQuorumVotes);
```

## Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

Saves `15 GAS, Per instance`

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L158C2-L158C91
- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L88
- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L1018

```solidity
File:

1018: if (_increase && (_collateral.totalDeposited + _amount) > _collateral.cap) revert VaultController_CapReached();
```

## [G-6] Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time. **Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. As an example, instead of repeatedly calling `someMap[someIndex]`, save its reference like this: `SomeStruct storage someStruct = someMap[someIndex]` and use it.

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L142

### Redundant External Self Calls

The referenced statements perform an external self-call as they specify `this.balanceOf` instead of `balanceOf` directly, instructing the compiler to treat the statement as an external call to the `address(this)` target. We advise the code to utilize `balanceOf` directly, avoiding the significant gas overhead of external calls. 

If the code wishes to be verbose about which implementation it invokes, the `this.balanceOf` statements can be replaced by `sUSD.balanceOf`, instructing the compiler to invoke the `sUSD` implementation of `balanceOf` explicitly.

- https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L131

```solidity
File: /contracts/core/USDA.sol

131: uint256 _balance = this.balanceOf(_msgSender());

141: uint256 _balance = this.balanceOf(_msgSender());

150: if (_susdAmount > this.balanceOf(_msgSender())) revert USDA_InsufficientFunds();
```