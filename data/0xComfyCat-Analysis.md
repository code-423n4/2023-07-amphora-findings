## Approach taken in evaluating the codebase
- Manual review while looking for common issue found in lending services

## Architecture recommendations
- Most view methods don't take pending interest into account. It would increase cost and effort for integrators to do it and possibility to do it wrong. Suggest to add view functions that include them.
- CurveMaster seems weird to have. It only use for single interest curve which is hard-coded as address zero in VaultController. This greatly reduce flexibility because it means that any number vault controllers will be able to use only one curve. Suggest merge CurveMaster functionality into VaultController so protocol can 1) have a curve per controller 2) save gas.

## Codebase quality analysis
- Since the project was based on previous audited codebases, it is hard to find bugs in the core contract (vault, lending) itself. Most bugs are from the modified/addition part.
- Lending part is not hard to understand. Logic is simple. Important checks such as solvency check is applied throughly.
- Reward part is more complicate. The Convex reward calculation is hard to visualize. Claimer contract is unnecessary complicated.
- Skipped the governance contract entirely. Too long.
- Oracle seems pretty solid. Curve read-only reentracy is protected against. With anchored view to check against absurd value. Inheritance is pretty neat.
- Severely lacked input validation.
- Gas wise is bad. It missed every common gas saving method. Although some part is understandable that it is trade-off for readability but for project that is meant to be deployed on mainnet, this is not good.
- Tests are bloated. Took so long to run. Pain. Even with that much fuzz it doesn't cover all the calculation issues. Suggest focus on possibility of scenario over complex fuzz test.

## Centralization risks
- Minimal centralization risks due to all contract being immutable and it is said that all parameters is going to be updated via governance.
- But some admin functions seems to be too dangerous (can break protocol) even for governance to use it.

## Mechanism review
- Scalability and integration might be an issue for WUSDA. There is a hard cap for WUSDA at 10m. Not sure what for but it will greatly limited potential of USDA. It should not be limited to be widely adopted. Let's say to use WUSDA as collateral somewhere else, an effort is required to integrate it and upside is only 10m? For integrators it is not worth doing it IMHO. Suggest to make this cap dynamic or remove it entirely.
- Liquidation is favored for borrowers since it only liquidate up to solvency not more. There won't be much buffer for protocol if price is falling quickly. Allowing over liquidate into safe collateralization ratio might be safer for protocol and disincentivize borrowers for aggressive borrowing.
- While integrating with Convex sounds good, I think this is kind of prohibits smaller users from using the protocol. Since adding many external calls for reward handling highly increase gas cost while Convex + Curve rewards is not high. It is not cost effective for smaller users.

### Time spent:
18 hours