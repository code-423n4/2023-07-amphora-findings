
USE 1E18 INSTEAD OF 10**18

In Solidiy, exponentiation operation (**) costs up to 10 gas. It is
possible to consume less gas to calculate token prices if DECIMAL variable
is fixed

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L1040C1-L1041C1

    _priceWithDecimals = _price * 10 ** (18 - _decimals);
  }


https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/WUSDA.sol#L35C1-L36C1

  uint256 public constant MAX_wUSDA_SUPPLY = 10_000_000 * (10 ** 18); // 10 M

