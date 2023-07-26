## Summary

The audit focused on the `Vault.sol`, `VaultController.sol`, and other relevant contracts of the Amphora Protocol. The goal was to assess the implementation's security and its integration with relevant protocols.


## Approach taken in evaluating the codebase

The audit involved a comprehensive review of the code and documentation, aiming to identify potential vulnerabilities and weaknesses. An attempt was made to explore various attack vectors and exploit any weaknesses in the contract logic. However, due to time constraints, some potential issues may remain undiscovered.


## Architecture Recommendations

The contracts generally demonstrate a well-designed architecture. However, some calculations and functionalities are not clear and may have potential issues. Notably, the use of hardcoded or immutable addresses in certain places, such as the `VaultController.sol`, could benefit from a more dynamic contract address approach. For example, the Convex `BOOSTER` contract address can migrate, so it is advisable not to hardcode its address. Instead, adding an option to update it dynamically would enhance flexibility and future-proof the protocol.

Additionally, in the `Vault.sol` contract, a dynamic address for the `VaultController.sol` contract should be considered to avoid potential complications when upgrading or migrating contracts.


## Codebase Quality Analysis

The codebase exhibits generally good quality, but there are areas where further attention is required. Some calculations and logic might be improved for clarity and robustness. Code review and documentation enhancements can contribute to making the contract more developer-friendly and secure.


## Mechanism Review

The mechanisms implemented in the contracts are sound, but further testing and auditing are necessary to ensure their resilience. Paying attention to edge cases and thoroughly testing various scenarios will enhance the robustness of the mechanisms.


## Systemic Risks

The systemic risks in the protocol appear to be well-managed. However, a more thorough examination of interest accrual mechanisms and potential shutdown scenarios is recommended to ensure their proper functioning under all circumstances.

### Convex Recommended Integration

- ✅ Stake LP tokens, CVX, or cvxCRV through the Booster contract: When using `Booster.deposit`, set `_stake = true` to stake LP tokens directly, or utilize `BaseRewardPool.stakeFor()` separately.

- ✅ Check staking rewards: Use appropriate methods like `BaseRewardPool.earned()` for LP tokens, cvxCRV, and CVX. For extra rewards from LP staking, fetch the `VirtualBalanceRewardPool` address via `BaseRewardPool.extraRewards(i)` and call `VirtualBalanceRewardPool.earned()`.

- ❎ Check for pool shutdown: Before initiating a deposit, check whether the relevant pool or the Booster contract is in shutdown status using `!Booster.poolInfo(i).shutdown && !Booster.isShutdown`.

- ❗️ Avoid hardcoding addresses: Since the Booster contract can migrate, consider implementing an option to update the address dynamically instead of hardcoding it.

- ✅ Withdraw staked funds: Use `BaseRewardPool.withdrawAndUnwrap(amount, claim=false)` to withdraw staked LP tokens, cvxCRV, or CVX. Note that claim determines whether to collect awards via `getReward()`, and using `withdraw()` instead of `withdrawAndUnwrap()` will return local Convex deposit tokens, not the LP tokens.


## Conclusion

The Amphora Protocol contracts exhibit a well-structured and thoughtful design. By addressing the recommended changes and conducting regular security assessments, the protocol can provide a reliable, secure, and user-friendly platform for users and developers alike. The integration with external protocols, particularly the Convex protocol, should be thoroughly tested to ensure the safety of users' funds and interactions with these protocols. It is essential to continue monitoring the contracts' performance and security and conduct further audits as the protocol evolves



### Time spent:
14 hours