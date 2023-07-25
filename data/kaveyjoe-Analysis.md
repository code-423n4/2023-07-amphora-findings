I have reviewed the codebase of Amphora Protocol, an auditing task related to a CDP borrow/lend protocol. The protocol enables users to deposit sUSD(v3) to lend and receive USDA, a rebasing token that automatically collects yield.

What I learned from reviewing this codebase:

Protocol Functionality: Amphora Protocol is a decentralized lending and borrowing platform. Users can lend their sUSD(v3) and receive USDA, which rebases to collect yield. On the other hand, users can borrow from the sUSD pool by opening a "vault" and depositing collateral, which can include both ERC-20 tokens and Curve LP tokens. The protocol seems to be well-designed to facilitate these functionalities.

Yield Collection Mechanism: The concept of the USDA token automatically rebasing to collect yield is interesting and has the potential to attract users looking for passive yield generation.

Integration with Convex: Amphora Protocol integrates with Convex, allowing users to deposit Curve LP tokens into relative pools on Convex. This enables users to continue earning CRV and CVX rewards while using those assets as collateral. The integration with Convex provides additional benefits to users and enhances the protocol's value proposition.

Governance and Reward Mechanism: The protocol takes a small fee from the CRV and CVX rewards and rewards the users with $AMPH, the protocol's governance token. This incentivizes users to actively participate in the protocol and contributes to its decentralization and community-driven nature.

Approach taken to evaluate the codebase:

Code Structure and Organization: I started by analyzing the code's structure and organization to understand how different components of the protocol are organized. This helps in identifying any potential code maintainability issues.

Smart Contract Security: Security is of utmost importance in DeFi protocols. I carefully audited the smart contracts for potential security vulnerabilities, such as reentrancy, arithmetic overflows/underflows, or improper access controls.

Token Economics: I analyzed the token economics of $AMPH, considering factors like total supply, token distribution mechanisms, vesting schedules (if any), and utility within the protocol.

External Dependencies: As the protocol integrates with Convex, I reviewed the integration and checked for any potential vulnerabilities or security concerns arising from these dependencies.

Documentation: I looked for comprehensive code documentation to understand the logic behind various functions and smart contract interactions.

Testing: I reviewed the test suite to ensure sufficient test coverage to verify the correctness and robustness of the code.

Code review resources used:

Etherscan and BSCScan: I used these block explorers to cross-check contract deployments, token information, and transaction history.

OpenZeppelin: OpenZeppelin's smart contract libraries are widely used and audited. I referred to their documentation and best practices for secure contract development.

Solidity Best Practices: I referred to industry-standard best practices for writing secure Solidity code, such as avoiding state mutations after external calls, using the latest compiler versions, and adhering to the Checks-Effects-Interactions pattern.

Time spent:

Given the complexity of the codebase and the importance of a thorough review, I spent approximately 30 hours on this analysis, spread over several sessions. As a computer engineering student, this review has allowed me to deepen my understanding of DeFi protocols, smart contract security, token economics, and integration with external protocols. It has also honed my skills in code auditing and reviewing complex smart contract systems, enhancing my abilities as a developer in the blockchain space.

### Time spent:
30 hours