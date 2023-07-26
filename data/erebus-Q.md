# First
In the comment above the modifier [onlyPauser](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L79C76-L79C81), it says `USDA._pauser()` but there is not such a function in the whole contract nor public variable. The variable is `address public pauser`, so consider removing the `_`

# Second
Instead of using [uint256 private constant MAX_UINT256 = 2 ** 256 - 1;](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/UFragments.sol#L62) and setting other constants which equals to builtin solidity's functionalities (redundancy), consider using `type(TYPE).max` or for cap variables