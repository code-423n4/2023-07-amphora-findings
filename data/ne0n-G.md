# Gas Optimizations
* Using modifiers can save gas
LoC: https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L335
The modifier `onlyGov` can be used here to save gas.
Modifier defined here: https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L106