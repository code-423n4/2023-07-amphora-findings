| | issue |
| ----------- | ----------- |
| 1 | [expressions for constant values such as a call to keccak256(), should use immutable rather than constant](#1-expressions-for-constant-values-such-as-a-call-to-keccak256-should-use-immutable-rather-than-constant) |
| 2 | [Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate](#2-multiple-addressid-mappings-can-be-combined-into-a-single-mapping-of-an-addressid-to-a-struct-where-appropriateunknown-ones) |
| 3 | [state variables should be cached in stack variables rather than re-reading them from storage](#3-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage) |
| 4 | [can make the variable outside the loop to save gas](#4-can-make-the-variable-outside-the-loop-to-save-gas) |
| 5 | [it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied](#5-it-costs-more-gas-to-initialize-non-constantnon-immutable-variables-to-zero-than-to-let-the-default-of-zero-be-applied) |
| 6 | [require() or revert() statements that check input arguments should be at the top of the function](#6-require-or-revert-statements-that-check-input-arguments-should-be-at-the-top-of-the-function) |
| 7 | [Ternary over if ... else](#7-ternary-over-if--else) |
| 8 | [should use arguments instead of state variable](#8-should-use-arguments-instead-of-state-variable) |
| 9 | [sort the modifiers of a function](#9-sort-the-modifiers-of-a-function) |
| 10 | [part of the code can be pre calculated](#10-part-of-the-code-can-be-pre-calculated) |
| 11 | [Use hardcoded address instead address(this)](#11-use-hardcoded-address-instead-addressthis) |
| 12 | [Divisions which do not divide by -X cannot overflow or overflow so such operations can be unchecked to save gas](#12-divisions-which-do-not-divide-by--x-cannot-overflow-or-overflow-so-such-operations-can-be-unchecked-to-save-gas) |
| 13 | [Unbounded Gas Consumption Risk Due to External Call Recipients](#13-unbounded-gas-consumption-risk-due-to-external-call-recipients) |

## 1. expressions for constant values such as a call to keccak256(), should use immutable rather than constant

- [GovernorCharlie.sol#L19](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L19)
- [GovernorCharlie.sol#L23](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L23)

- [USDA.sol#L21](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L21)

- [UFragments.sol#L86](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L86)
- [UFragments.sol#L87](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L87)
- [UFragments.sol#L89](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L89)


## 2. Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate(unknown ones)

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. 

`balances` and `isTokenStaked` can be put together to reduce access cost because they are used together in some functions.(also if balances is defined a bit smaller they can be put together to slots used by 1 because `isTokenStaked` is a bool)
- [Vault.sol#L43-L46](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L43-L46)


## 3. state variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. 


`enabledTokens[_i]` should be cached before the #L869 and inside the for loop to reduce a complex SLOAD. if cached before instead of after in #L869 we can use the cached version instead
- [VaultController.sol#L874](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L874)

same as above here 
- [VaultController.sol#L902](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L902)

`balances[_token]` is being read twice and wasting gas. we can cache it before #L93 and use that one in #L103 and change the += later to a simple equals and use the cache version there to save gas
- [Vault.sol#L106](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L106)

`balances[_tokenAddress]` can be cached before #L142 to reduce 2 complex SLOADs in lines 148 and 150
- [Vault.sol#L148](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L148)

`reserveAmount` can be cached to possibly save 97 gas if the condition be true, if its false we only waste 3 gas compared to saving 97 if condition be true.
- [USDA.sol#L132](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L132)

`reserveAmount` same thing as above
- [USDA.sol#L142](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L142)

`_allowedFragments[msg.sender][_spender]` can be cached before #L284 and use the cached versions in #L 284 and #L286 to reduce one complex SLOAD
- [UFragments.sol#L286](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L286)

`(_subtractedValue >= _oldValue) ? 0 : _oldValue - _subtractedValue` value should be put inside a stack variable first then put into `_allowedFragments[msg.sender][_spender]` that way we can use the cached verssion and reduce one complex SLOAD
- [UFragments.sol#L89](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L89)

`curves[_tokenAddress]` can be cached before line #L23 to reduce one complex SLOAD. and if the condition is false and we revert we only lose 3 gas but otherwise save a lot
- [CurveMaster.sol#L23](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L23)

`vaultControllerAddress` can be cached to possibly save 97 gas with only risking losing 3, if the condition in #L42 is true we save 97 gas otherwise only lose 3
- [CurveMaster.sol#L42](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L42)


## 4. can make the variable outside the loop to save gas

make the variable outside the loop and only give the value to variable inside

- [VaultController.sol#L995](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L995)
- [VaultController.sol#L996](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L996)
- [VaultController.sol#L869](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L869)
- [VaultController.sol#L874](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L874)
- [VaultController.sol#L876](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L876)
- [VaultController.sol#L879](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L879)
- [VaultController.sol#L882](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L882)
- [VaultController.sol#L897](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L897)
- [VaultController.sol#L902-L910](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L902-L910)

- [Vault.sol#L170](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L170)
- [Vault.sol#L176](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L176)
- [Vault.sol#L177](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L177)
- [Vault.sol#L189-L191](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L189-L191)
- [Vault.sol#L255-L257](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L255-L257)


## 5. it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied

state var is already 0, no need to reassign it to 0
- [GovernorCharlie.sol#L94](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L94)

stack var is already 0
- [GovernorCharlie.sol#L259](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L259)
- [GovernorCharlie.sol#L313](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L313)
- [GovernorCharlie.sol#L382](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L382)


## 6. require() or revert() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

cheaper reverts should be on top and the most expensive ones should be on top.

put the first revert to last because its the most expensive
- [GovernorCharlie.sol#L338-L340](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L338-L340)

put the first one as last
- [USDA.sol#L148-L150](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L148-L150)


## 7. Ternary over if ... else

Using ternary operator instead of the if else statement saves gas.

- [VaultController.sol#L549-L554](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L549-L554)

- [GovernorCharlie.sol#L346-L347](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L346-L347)

- [AnchoredViewRelay.sol#L83-L87](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L83-L87)


## 8. should use arguments instead of state variable

This will save 100 gas because it gets rid of a storage read and instead uses a argument that we already have which gives the same result

use `_proposalTimelockDelay` instead of `proposalTimelockDelay`
- [GovernorCharlie.sol#587](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L587)

instead of `emergencyTimelockDelay` use `_emergencyTimelockDelay`
- [GovernorCharlie.sol#598](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L598)

instead of `votingDelay` use `_newVotingDelay`
- [GovernorCharlie.sol#609](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L609)
- [GovernorCharlie.sol#620](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L620)

instead of `emergencyVotingPeriod` use `_newEmergencyVotingPeriod`
- [GovernorCharlie.sol#631](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L631)

instead of `quorumVotes` use `_newQuorumVotes`
- [GovernorCharlie.sol#653](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L653)

instead of `emergencyQuorumVotes` use `_newEmergencyQuorumVotes`
- [GovernorCharlie.sol#664](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L664)

instead of `whitelistGuardian` use `_account`
- [GovernorCharlie.sol#688](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L688)

instead of `optimisticVotingDelay` use `_newOptimisticVotingDelay`
- [GovernorCharlie.sol#699](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L699)

instead of `optimisticQuorumVotes` use `_newOptimisticQuorumVotes`
- [GovernorCharlie.sol#710](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L710)


## 9. sort the modifiers of a function

with changing the position that modifiers are used for a function there is possibility to save gas if the cheaper modifier returns error.

`paysInterest` modifier actions are more expensive than `onlyOwner` so swap the places for possible gas save
- [USDA.sol#L161](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L161)
- [USDA.sol#L182](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L182)

`paysInterest` modifier actions are more expensive than `whenNotPaused` so swap the places for possible gas save
- [USDA.sol#L101](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L101)
- [USDA.sol#L147](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L147)
- [USDA.sol#L202](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L202)


## 10. part of the code can be pre calculated

these parts of the code can be pre calculated and given to the contract as constants this will stop the use of extra operations
even if its for code readability consider putting comments instead.

`INITIAL_FRAGMENTS_SUPPLY * 10 ** 48` reduces one ** operation and one multiplication
- [UFragments.sol#L100](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L100)

`keccak256(bytes(NAME))` reduces one keccak256() operation and a cast because `Name` is a constant and does not change
- [GovernorCharlie.sol#509](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L509)

`keccak256(bytes(EIP712_REVISION))` reduces one keccak256() operation and a cast because `EIP712_REVISION` is a constant and does not change
- [UFragments.sol#L169](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L169)

`_TWENTY_FIVE_THOUSANDS * BASE_SUPPLY_PER_CLIFF` reduces one multiplication
- [AMPHClaimer.sol#L195](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L195)

`BASE_SUPPLY_PER_CLIFF * _FIFTY` reduces one multiplication
- [AMPHClaimer.sol#L196](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L196)

`365 days + 6 hours` turn into days or hours instead, reduces one use of summation
- [Vault.sol#L948](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L948)


## 11. Use hardcoded address instead address(this)

Instead of using `address(this)`, it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry's `script.sol` and solmate's `LibRlp.sol` contracts can help achieve this. - [Reference](https://twitter.com/transmissions11/status/1518507047943245824)

- [GovernorCharlie.sol#107](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L107)
- [GovernorCharlie.sol#335](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L335)
- [GovernorCharlie.sol#509](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L509)
- [GovernorCharlie.sol#716](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L716)

- [Vault.sol#L92](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L92)
- [Vault.sol#L177](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L177)
- [Vault.sol#L182](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L182)
- [Vault.sol#L191](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L191)
- [Vault.sol#L210](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L210)
- [Vault.sol#L245](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L245)
- [Vault.sol#L257](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L257)
- [Vault.sol#L271](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L271)

- [USDA.sol#L103](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L103)
- [USDA.sol#L206](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L206)
- [USDA.sol#L215](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L215)

- [UFragments.sol#L57](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L57)
- [UFragments.sol#L105](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L105)
- [UFragments.sol#L169](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L169)

- [AMPHClaimer.sol#L166](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L166)

- [WUSDA.sol#L215](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L215)


## 12. Divisions which do not divide by -X cannot overflow or overflow so such operations can be unchecked to save gas

Make such found divisions are unchecked when ensured it is safe to do so

- [VaultController.sol#L948](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L948)

- [Vault.sol#L343](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L343)

- [AMPHClaimer.sol#L88](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L88)
- [AMPHClaimer.sol#L153](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L153)
- [AMPHClaimer.sol#L177](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L177)
- [AMPHClaimer.sol#L196](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L196)
- [AMPHClaimer.sol#L222](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L222)
- [AMPHClaimer.sol#L250](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L250)

- [WUSDA.sol#L255](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L255)

- [ThreeLines0_100.sol#L86](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol#L86)

- [ChainlinkTokenOracleRelay.sol#L51](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L51)

- [WstEthOracle.sol#L32](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/WstEthOracle.sol#L32)

- [UniswapV3TokenOracleRelay.sol#L37](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3TokenOracleRelay.sol#L37)


## 13. Unbounded Gas Consumption Risk Due to External Call Recipients

In the context of Solidity, external function calls without a specified gas limit present a significant risk. The callee contract has the potential to consume all the gas allocated to the transaction, causing an undesired revert and disrupt the function's execution. To mitigate this, it's recommended to explicitly set a gas limit when performing external calls using `addr.call`{gas: }. This limits the gas forwarded to the callee, preventing potential pitfalls and offering better control over transaction execution.

- [GovernorCharlie.sol#350](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L350)

