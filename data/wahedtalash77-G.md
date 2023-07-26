## [G-01] For uint use != 0 instead of > 0
558: `if (_fee > 0) usda.vaultControllerMint(owner(), _fee);`
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L558

660: ` if (_tokenAmount > 0) _tokensToLiquidate = _tokenAmount;`
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L660

206: `if (_totalCrvReward > 0 || _totalCvxReward > 0) {`
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L206

```
223: if (_totalCvxReward > 0) CVX.transfer(_msgSender(), _totalCvxReward);
224: if (_totalCrvReward > 0) CRV.transfer(_msgSender(), _totalCrvReward);
```
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/Vault.sol#L223C6-L224C76

## [G-02]  Avoiding Initialization of Loop Index If It Is 0 and Avoid unnecessary read of array length in for loops can save gas
259: `for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {`
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L259

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L382 

## [G-03] unchecked { ++i } is more gas efficient than i++ for loops
259: `for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {`
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L259	
## [G-04] Sort Solidity operations using short-circuit mode
Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 
	//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L469C5-L473C37

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L158

## [G-05 ]  The result of a function call should be cached
```
function balanceOf(address _who) external view override returns (uint256 _balance) {
    return _gonBalances[_who] / _gonsPerFragment;
  }
```
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/UFragments.sol#L128C3-L130C4

## [G-06] Optimize names to save gas
>Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions.

>See more [here](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).



## [G-07] Use calldata instead of memory for function arguments that do not get mutated
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L120C1-L126C5

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L139C2-L147C4

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L160C1-L165C20

## [G-08] If statements that use && can be refactored into nested if statements
208: ` if (_emergency && !isWhitelisted(msg.sender)) {`
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L208

```
158: if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L158

## [G-9] Rearranging order of state variable declarations to pack them will save storage slots and gas
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L17C1-L35C40

## [G-10] Use a more recent version of solidity
In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.