# First
[Here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/USDA.sol#L259) the function `donation` makes the math to share the donation between all the `USDA` holders. However, if it get to be more than `MAX_SUPPLY`, the `_totalSupply` is set to `MAX_SUPPLY` and it will be doing unnecessary instructions in the `_gonsPerFragment` calculation for the following calls to donation from within the contract (if `_totalSupply` is not reduced). The optimization lies lies in adding an `if (_totalSupply == MAX_SUPPLY) return;` so that it does not spend gas doing redundant instructions when the supply has reached the limit. 

# Second
It is more efficient, for `++X` and `X++` to do `X += 1`. See the next test in foundry to show that it saves 8 gas and 2 gas respectively:

```
pragma solidity ^0.8.13;

import "forge-std/Test.sol";

contract POC is Test {
    uint256 private a;

    function setUp() public {
        a = 0;
    }

    function testA() public {
        a++;
    }

    function testB() public {
        a += 1;
    }

    function testC() public {
        ++a;
    }
}
```

Results:

- testA -> 22397
- testB -> 22389
- testC -> 22391

Consider using the `X += 1` version everywhere. The RE can be `[^ \+]*\+\+` for the occurrences of `X++` and `\+\+[^ \)]*` for the occurrences of `++X`. The same applies to other arithmetic operations.

# Third 
Consider making [MAX_SUPPLY](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/UFragments.sol#L70C18-L70C28) constant, which saves storage read/writes (ssread/sstore costs A LOT of gas) from being done within the contract's logic (and saves more opcodes, which reduces its size). The same goes to many others like [name](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/UFragments.sol#L76) and [symbol](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/UFragments.sol#L77)

# Fourth
Consider changing all the `strings` to `bytes` because they are packed more densely in memory, resulting in less gas usage and a smaller contract size. The reason behind is that strings are dynamically-sized but bytes have a fixed size

# Fifth
As what I have said in the first Gas submission, change [_nonces[_owner] = _ownerNonce + 1;](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/UFragments.sol#L336) to `_nonces[_owner] += 1;`