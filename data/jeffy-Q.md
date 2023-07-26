# variable that was never set in the contract is used in revert condition

relevant lines:
- [initialProposalId](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L44) in the [revert condition](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L463C40-L463C51) 

the condition checks if `_proposalId` is less than `initialProposalId`, and because `initialProposalId` is `uint256`, the condition is never true unless both of them is 0, this would cause the contract not to revert when it should.

# variable that was never set in the contract yet is referenced in many places

relevant lines:
- [proposalReceipts](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L77) was never set but is used [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L453) and [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L539)

cheers