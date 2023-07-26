
- There are no checks to prevent proposers from re-submitting the same proposal multiple times, which could lead to spamming the system:

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L120

- It has happened in past that Chainlink return 0 price for as asset. It is recommented to check `_latest` it it equals to zero.

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L75-L76

- In `UniswapV3OracleRelay.sol`, the constructor function should check to see if the `_lookback` parameter is greater than zero. This could be done by checking if the `_lookback` parameter is greater than or equal to 1.

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L42-L43

- The _getPriceFromUniswap function should check to see if the _arithmeticMeanTick parameter is valid. This could be done by checking if the _arithmeticMeanTick parameter is within the range of valid tick values.

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L79-L82

- Chainlink price oracle fails to check the roundId, and might return the stale price.

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L57-L60

- I believe following function will do more harm to the protocol and should be removed (since we already have the `removeVaultController()` function)

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/USDA.sol#L297-L301

- The codebase fails to use `safeApprove` & `safeTransfer` at several places similar to:

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/Vault.sol#L321

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/USDA.sol#L216

- The functions `USDA.vaultControllerDonate` and `USDA.vaultControllerBurn` fails to include the `whenNotPaused`, which can ruin the accounting/functioning of the protocol.

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/USDA.sol#L253-L255

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/USDA.sol#L231-L234

- Unnecassary `payable` in VaultDeployer's constructor. This can lead to funds getting stuck in the contract.

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultDeployer.sol#L18-L19

- The deployVault function should check to see if the _id is already in use. This could be done by checking if there is already a vault with the same _id.

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultDeployer.sol#L26-L28