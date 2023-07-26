# Analysis  -🏺Amphora Project 🏺
### Summary
| List |Head |Details|
|:--|:----------------|:------|
|a) |The approach I followed when reviewing the code | Stages in my code review and analysis |
|b) |Analysis of the code base | What is unique? How are the existing patterns used? |
|c) |Test analysis | Test scope of the project and quality of tests |
|d) |Architectural | Architecture feedback |
|e) |Documents  | What is the scope and quality of documentation for Users and Administrators? |
|f) |Centralization risks | How was the risk of centralization handled in the project, what could be alternatives? |
|g) |Systemic risks | Potential systemic risks in the project |
|h) |Competition analysis| What are similar projects? |
|i) |Security Approach of the Project | Audit approach of the Project |
|j) |Other Audit Reports and Automated Findings | What are the previous Audit reports and their analysis |
|k) |Gas Optimization | Gas usage approach of the project and alternative solutions to it |
|l) |New insights and learning from this audit | Things learned from the project |


## a) The approach I followed when reviewing the code

First, by examining the scope of the code, I determined my code review and analysis strategy.
https://github.com/code-423n4/2023-07-amphora#scope

Accordingly, I analyzed and audited the subject in the following steps;

| Number |Stage |Details|Information|
|:--|:----------------|:------|:------|
|1|Compile and Run Test|[Installation](https://github.com/code-423n4/2023-07-amphora#tests)|Test and installation structure is simple, cleanly designed|
|2|Architecture Review| [Amphora Protocol](https://amphora-protocol.gitbook.io/amphora-protocol) |Provides a basic architectural teaching for General Architecture|
|3|Graphical Analysis  |Graphical Analysis with [Solidity-metrics](https://github.com/ConsenSys/solidity-metrics)|A visual view has been made to dominate the general structure of the codes of the project.|
|4|Slither Analysis  | [Slither](https://github.com/crytic/slither)| |
|5|Test Suits|[Tests](https://github.com/code-423n4/2023-07-amphora#tests)|In this section, the scope and content of the tests of the project are analyzed.|
|6|Manuel Code Review|[Scope](https://github.com/code-423n4/2023-07-amphora#scope)|According to the architectural design, the codes were examined starting from the "Hermes, which is the main starting point.|
|7|Infographic|[Figma](https://www.figma.com/)|I made Visual drawings to understand the hard-to-understand mechanisms|
|8|Special focus on Areas of  Concern|[Areas of Concern](https://github.com/code-423n4/2023-07-amphora#additional-context)||

## b) Analysis of the code base

#### VaultController.sol
Contract is the VaultController contract for the Amphora protocol. Amphora is a CDP borrow/lend protocol, which means that users can deposit sUSD(v3) to lend, and in return receive USDA, a rebasing token that automatically rebases collecting yield. To borrow from the sUSD pool, users open a "vault" and deposit collateral. 

Each vault is unique to a user, keeping isolated positions, and it can receive both ERC-20 tokens and Curve LP tokens. Curve LP tokens are deposited into their relative pools on Convex, so that users continue to earn CRV and CVX rewards while using the assets as collateral.

The VaultController contract is responsible for managing the vaults on the Amphora protocol. It allows users to open and close vaults, deposit and withdraw collateral, and borrow sUSD. The contract also implements a number of safety features, such as liquidation of vaults that are over-leveraged.

 VaultController contract:

- openVault: This function allows a user to open a new vault.
- closeVault: This function allows a user to close an existing vault.
- depositCollateral: This function allows a user to deposit collateral into a vault.
- withdrawCollateral: This function allows a user to withdraw collateral from a vault.
- borrowSUSD: This function allows a user to borrow sUSD from a vault.
- liquidateVault: This function liquidates a vault that is over-leveraged.
- The VaultController contract is an important part of the Amphora protocol, and it ensures that the protocol is secure and that users can safely borrow and lend sUSD.

####  GovernorCharlie.sol
The GovernorCharlie contract for the Amphora protocol. GovernorCharlie is a governance contract that allows Amphora users to propose and vote on changes to the protocol.

The contract is based on the Compound Governor contract, and it implements a number of features that allow for secure and democratic governance. These features include:

- Proposal voting: Users can propose changes to the protocol, and other users can vote on these proposals.
- Quorum: A proposal must meet a certain quorum of votes before it can be passed.
- Voting weight: The voting weight of each user is based on the amount of USDA they hold.
- Treasury: The GovernorCharlie contract controls the Amphora treasury, and it can be used to fund proposals that are passed by the community.
The GovernorCharlie contract is an important part of the Amphora protocol, and it ensures that the protocol is governed in a democratic and transparent way.

#### Vault.sol
The Vault contract is a core component of the Amphora protocol. It allows users to open and manage vaults, which are used to borrow and lend sUSD. Each vault is unique to a user, and it can hold both ERC-20 tokens and Curve LP tokens.

#### USDA.sol
The USDA contract is a core component of the Amphora protocol. It is a rebase token that automatically rebases collecting yield. The contract is based on the OpenZeppelin ERC20Rebase contract, and it implements a number of features that allow for secure and transparent rebase mechanics.


## c) Test analysis
What did the project do differently? ;
The project has written an effective invariant test suite,
invariant tests; Rather than describing properties of specific functions, it defines "immutable properties" about a particular contract or system of contracts that should always apply. These are things like "this vault contract always holds enough coins to cover all withdrawals", "x*y in a Uniswap pool always equals k" or "the total supply of this ERC20 token always equals the sum of all individual balances" it could be.

For example, invariant test was applied in the USDA.t.sol test file in the project and it was tested with the logic that totalSupply and total used tokens should be equal;

```solidity
core\solidity\test\invariant\USDA.t.sol:
  40    /// @dev the sum of all deposits minus the withdrawals, minus the donated amount should be equal to the total supply of USDA
  41:   function invariant_theSumOfDepositedMinusWithdrawnShouldBeEqualToTotalSupply() public view {
  42:     uint256 _sUSDInTheSystem = usdaHandler.ghost_depositSum() - usdaHandler.ghost_withdrawSum();
  43: 
  44:     uint256 _totalMintedUsda = usdaHandler.ghost_mintedSum() - usdaHandler.ghost_burnedSum();
  45: 
  46:     uint256 _usdaTotalSupplyInitialWithoutDonations = usda.totalSupply() - usdaHandler.initialFragmentsSupply();
  47:     uint256 _usdaTotalSupplyInitialWithDonations =
  48:       _usdaTotalSupplyInitialWithoutDonations - usdaHandler.ghost_donatedSum();
  49: 
  50:     uint256 _totalSupply = _usdaTotalSupplyInitialWithDonations > _totalMintedUsda
  51:       ? _usdaTotalSupplyInitialWithDonations - _totalMintedUsda
  52:       : _totalMintedUsda - _usdaTotalSupplyInitialWithDonations;
  53: 
  54:     assert(_sUSDInTheSystem == _totalSupply);
  55:   }
```


What could they have done better?;
There are many unit tests in the project, integration tests in which the interaction of contracts with each other are modeled should be increased.



## d) Architectural 
Amphora's core is based on a fork of the "Interest Protocol" by Gfx Labs
The basis of the architecture is based on the "Capital Efficiency of Interest Protocol";

https://interestprotocol.io/book/docs/concepts/Borrowing/CapitalEfficiency/

#### Interest Rates

All USDi borrowers pay a single borrowing interest rate that varies depending on the current reserve ratio. The interest paid by borrowers, net of a protocol fee, is distributed pro rata to all USDi holders.

#### Reserve Ratio

The *reserve ratio* is defined as

$$
\frac{\text{USDC in protocol reserve}}{\text{USDi total supply}}
$$

The reserve ratio measures the amount of USDC liquidity that the protocol currently has to meet the potential redemption demand of USDi holders.

#### Interest Rate
The borrow APR is a decreasing function of the reserve ratio and is defined as follows:



The function has two kinks, $(s_1,r_1)$ and $(s_2,r_2)$. The interval $[s_1,s_2]$ is the range of reserve ratios that the protocol targets. The current parameters are provided in the table below.

| $s_1$ | $s_2$ |    | $r_1$ | $r_2$ | $r_3$|
|-------|-------|----|-------|-------|------|
| 60%   | 40%   |    | 0.5%  | 10%   | 600% |



![image](https://github.com/code-423n4/2023-07-amphora/assets/104318932/cd01673a-8580-4633-a418-7f256ea9b097)






## e) Documents 
Documentation should be increased further, it is recommended to add the architectural design to the documents as infographic


##  f) Centralization risks 
There is a risk of centrality in the project as the onlyOwner
The owner role has a single point of failure and onlyOwner can use critical functions, posing a centralization issue. There is always a chance for owner keys to be stolen, and in such a case, the attacker can cause serious damage to the project due to important functions.

The code detail of this topic is specified in automatic finding;
https://gist.github.com/thebrittfactor/0cfcacbd1366b927c268d0c41b86781b#m-07-centralization-issue-caused-by-admin-privileges  

##  g) Systemic risks 
The most important system risk in the project; Risks arising from the use of Oracle, the project uses Chainlink, Curve and Uniswap V3 oracles, security exploits from oracles have been increasing recently, so using Oracle is a risk in itself and it is important to minimize this risk.


## h) Competition analysis
Similar protocols ; Interest Protocol, Convex and Synthetix
Amphora's core is based on a fork of the "Interest Protocol" by Gfx Labs. They use a math model called 3 lines to manage interest rates. Robust documentation can be found here: https://interestprotocol.io/book/docs/concepts/Borrowing/CapitalEfficiency/ and here https://interestprotocol.io/book/docs/concepts/Borrowing/InterestRates/


## i) Security Approach of the Project

Successful current security understanding of the project;
1- They manage the 2nd audit process with an innovative audit such as Code4rena, in which many auditors examine the codes.



What the project should add in the understanding of Security;
1- By distributing the project to testnets, ensuring that the audits are carried out in onchain audit. (This will increase coverage)
2- After the project is published on the mainnet, there should be emergency action plans (not found in the documents)
3- After the inspection, the concept of "continuous inspection" should be adhered to with bug bounty programs such as ImmuneFi.


## j) Other Audit Reports and Automated Findings 

**Automated Findings:**
https://gist.github.com/thebrittfactor/0cfcacbd1366b927c268d0c41b86781b

Especially Medium-High detections in the Automated Finding Report should be taken into account;

### High Risk Issues
|Id|Title|Instances|
|:--:|:-------|:--:|
|[[H-01]](#h-01-missing-slippage-checks)| Missing slippage checks | 4 |

Total: 4 instances over 1 issues.

### Medium Risk Issues
|Id|Title|Instances|
|:--:|:-------|:--:|
|[[M-01]](#m-01-missing-staleness-checks-for-chainlink-oracle)| Missing staleness checks for Chainlink oracle | 2 |
|[[M-02]](#m-02-chainlink-oracle-will-return-the-wrong-price-if-the-aggregator-hits-minanswer)| Chainlink oracle will return the wrong price if the aggregator hits `minAnswer` | 2 |
|[[M-03]](#m-03-return-values-of-transfertransferfrom-not-checked)| Return values of `transfer`/`transferFrom` not checked | 2 |
|[[M-04]](#m-04-some-functions-do-not-work-correctly-with-fee-on-transfer-tokens)| Some functions do not work correctly with fee-on-transfer tokens | 2 |
|[[M-05]](#m-05-some-erc20-can-revert-on-a-zero-value-transfer)| Some `ERC20` can revert on a zero value `transfer` | 1 |
|[[M-06]](#m-06-non-compliant-ierc20-tokens-may-revert-with-transfer)| Non-compliant `IERC20` tokens may revert with `transfer` | 2 |
|[[M-07]](#m-07-centralization-issue-caused-by-admin-privileges)| Centralization issue caused by admin privileges | 26 |

## k) Gas Optimization
The project is generally efficient in terms of gas optimizations, many generally accepted gas optimizations have been implemented, gas optimizations with minor effects are already mentioned in automatic finding, but gas optimizations will not be a priority considering code readability and code base size




## l) New insights and learning from this audit 

- Invariant tests are of high quality within the scope of the project's testing, I have gained deep knowledge about this situation, which I have rarely seen in projects before.
- Amphora's core is based on a fork of the "Interest Protocol" by Gfx Labs.I learned detailed information about this topic.
- Using Chainlink and Uniswap v3 oracle in CDP borrow/lend protocols


### Time spent:
12 hours