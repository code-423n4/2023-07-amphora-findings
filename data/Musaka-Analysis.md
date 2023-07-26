# Summary
Amphora has good decentralization (*mainly on the fact that they have a DAO*), reward staker that uses Convex (*crv optimizer*), a re-basing token and as always a wrapper to that re-basing token, and finally, but most importantly a borrowing/liquidation mechanism.  All this combined makes a full protocol that user's would like to use.

| Severity | Occurrences |
|----------|------------|
| High     | 2          |
| Medium   | 3          |
| Low      | 5          |
| Gas      | 0         |

# Time allocations

|            |            |
|------------|------------|
| Start date | 21.07.2023 |
| End date   | 26.07.2023  |
| Total      | 5 days     |

| Members       | Positions            | Time spent |
|---------------|----------------------|------------|
| **0x3b**      | full time researcher | 30H+       |
| **ZdravkoHr** | full time researcher | 25H+       |

# Findings and how we found them

As always, various types of findings were submitted for review. These findings can be categorized as logical errors, code errors, and recommendations. However, due to the nature of the protocol and the multitude of concepts that Amphora implemented simultaneously, like liquidations, wrapping of tokens and governance, the allocated time was not enough for us to perform a deep dive in the audit.

# Codebase quality
Code was of exceptional quality. Although it included many different concepts, that are sometimes hard to combine, it still manged to utilize them all and even improve on them. 
Here are a few examples of high quality solidity code that Amphora had:
- It used `if()` statements instead of `require()` to save on gas.
- Medium size functions **-** functions being too big (120 lines+) is bad, because you can't comprehend what the function does, and if they are too small and too fractured it also get's in the way of aduting, because you need to constantly check folders and filed for diffrent functions
- Recover functions **-** to prevent any ERC20 from getting stuck in the contract
- Pausable state **-** always good if an emergency happens
- Helper functions for liquidators
- Amazing oracle interfaces that can be matched with any ERC20 and be made to used Chainlink or UNI TWAP

# Mechanism review

###### Staking && Rewards
The staking in Amphora is actually quite decent, it utilizes the **CRV** optimizer Convex and has it's own set of oracle price extractors that can be made to use [ChainLink](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol) or UNI's [TWAP](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol), this flexibility gives it extra security and expands on the future opportunities that Amphora can upgrade to (like using the TWAPs to target UNI liquidity or accept **ETH** and use an optimizer for Lido). 

The only thing I don't like, is that the system is not letting users opt for **CRV** and **CRX**, but directly it [claims](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L206-L221) them and sends them to **AMPHClaimer** to be swapped for** AMPH**. I know this is on purpose, but I am not sure if most of the users are gonna prove of it.

###### DAO
The DAO module of the protocol brings to life taken decisions to the Ethereum network in the form of proposals. The several types of proposals - emergency, optimistic and standard - combined with the whitelisting of particular users, bring more flexibility to the whole process. The DAO does a good job, as on top of that, it adds the ability to cancel propsals by their owners or by anyone if the proposal's owner's voting power drops below a certain threshold. 

###### USDA && wUSDA
Behind these two stands **sUSD** (Synthetiscs stable), tho I am not sure why use such a small cap stable when you have the option for bigger ones (**USDC**, **DAI**, **FRAX** and so on...). The re-basing mechanisms of **USDA** with some help of  [UFragments](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol) was on spot. Now **wUSDA** was not well converged with **USDA** as  the math was rough and way too simple.

###### Oracles
Though some implementation of these oracles were unclear how they would be done and what the return values were gonna be in term of decimals, we were quite amazed at the fact that it provided so many options for Amphora to use. Example is the decentralization of UNI's TWAP and the accuracy of ChainLink's direct feed, bolt an option for an ERC20 to use. On top of that it had  extensions supporting Compound for the prices of cTokens and also some oracles that extracted LP prices from CRV.

# Centralization risks
Looking at the decentralized nature of the protocol, there are a few concerns worth considering. For example, in [VaultController.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol), there are admin functions for changing the configuration of the smart contract (such as tokens, curveMaster, etc...). These functions however can be called by the owner of the vault unlimited amount of times. A good approach would be either restricting the functions to be called an arbitrary amount of times at maximum, or adding some mechanism that prohibits changing the certain config if a given amount of time has not passed since the last change.

# Systemic risks
Here we are presented with 2 types of risk code and DEFI implementations.
###### Code risk:
The code before and after the audit are gonna be 2 diffrent things. Now Amphora looks solid enough and hopefully after the audit it it will be ready to be launched on main-net, without having any major bugs in it. Amphora's infrastructure seems good en enough to support future updates/add-ons without having to change too much of it's code. It has a well thought out liquidation mechanics, that from our perspective seem good (for now much of the values are unknown, like LTV, max cap and so on).

###### DEFI risk:
Now this is the interesting part as Amphora is gonna be one of the many staker that borrowers and liquidity providers can choose from. Main issue it faces is gonna be the rough competition in the field, users are gonna wonder why choose Amphora over something simple and secure, like Maker or AAVE. And the only reason they will choose this protocol over the big names is if the incentive is right. This is the bad side of the liquidity market, you are either **big and safe** or you provide such **APYs** that they out-wight the risk for some users and they come to you. Tho high **APYs** is impossible to sustain as the money needs to come from somewhere, Anchor provided a high APY to attract investors, close to 20%, but as we saw it didn't manage to sustain it over the long term and it burned to the ground when there was just no more money to be sent to staker.

### Time spent:
54 hours