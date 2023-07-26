# [L-01] No way to deregister a collateral token

Within the current `VaultController` implementation there are functions to register and and update collateral tokens. However, there is no function to deregister (remove) a collateral token. This can be a problem for various reasons - for example, the price feeds for a particular collateral asset might become unreliable and start returning wrong prices.

## Lines of code

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L331-L374

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L383-L412

# [N-01] Unused imports

## Lines of code

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/Vault.sol#L4

# [N-02] Redundant return value checks

The following functions are being called in a few places within the `Vault` contract. In some of those places their (boolean) return values are being validated. However, this is redundant, as those functions always return `true`. This can be seen in their implementations:

https://github.com/convex-eth/platform/blob/main/contracts/contracts/BaseRewardPool.sol#L263-L279

https://github.com/convex-eth/platform/blob/main/contracts/contracts/Booster.sol#L250-286

## Lines of code

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/Vault.sol#L120

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/Vault.sol#L297

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/Vault.sol#L322

# [N-03] Missing NatSepc param tags for some function parameters

`VaultController::registerErc20` - has a missing @param tag for `_poolId`

## Lines of code

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L324-L338

# [N-04] Redundant multiple assignments

`VaultController::registerErc20` - The assignment of `_collateral.poolId` can be made on a single line

## Lines of code

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L348

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L352

# [N-05] Vague variable names

`VaultController::liquidateVault` - a better name for `_toLiquidate` would be `_liquidated` or `_liquidatedTokens`
`VaultController:calculateInterest` and `VaultController:_payInterest` - a better name for `_interest` would be `_interestIncrease`

## Lines of code

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L650

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L921

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L928
