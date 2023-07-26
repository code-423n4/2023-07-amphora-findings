# Amphora Protocol - Quality Assurance Report

**Note: Some issues are seems to be mentioned in the automated findings report but the context is different and for similar context report missed to mentioned all of those instances in the code which can be missed by development team and they can deploy vulnerable code.**

# Low Risk Findings

## Table Of Content

| Number                                                                                    | Issue                                                                         | Instances |
| :---------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------- | --------: |
| [L-01](#l-01---use-the-fixed-pragma-version)                                              | Use the fixed pragma version                                                  |        52 |
| [L-02](#l-02---missing-validation-for-address0-in-the-constructors)                       | Missing validation for `address(0)` in the constructors                       |         8 |
| [L-03](#l-03---missing-validation-for-address0-in-the-functions-changing-state-variables) | Missing validation for `address(0)` in the functions changing state variables |        10 |
| [L-04](#l-04---execution-throw-underflow-error)                                           | Execution throw `underflow` error                                             |         2 |
| [L-05](#l-05---abiencoderv2-is-not-experimental-anymore)                                  | `ABIEncoderV2` is not experimental anymore                                    |         2 |

### [L-01] - Use the fixed pragma version.

**Details**

Pragma versions are designed in a way that `0.x.0` introduces bracking changes and `0.0.x` introduces bug fixes in the previous versions.

Now using `^` with the pragma vesions opens the code compilation till the next `< 0.x.0` version, there will be no breaking changes until version `0.x.0`, you can be sure that your code compiles the way you intended but due to the exact version of the compiler is not fixed, newly introduced bugfix can still affect the code.

It is recommeded by [Solidity Docs](https://docs.soliditylang.org/en/v0.8.21/layout-of-source-files.html#version-pragma) to use fixed version for your projects.

Which version should we choose?

There are pros and cons in using both older and newer versions and we should always use the latest stable(tested) version.

Newer versions fix the bugs, introduces new ways to optimize the code and release new features but can introduces new bugs as well.

Older versions are battle tested but lacks the pros of newer versions.

According to my knowledge until now `0.8.17` is the recommended choice.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[AMPHClaimer.sol - Line 02](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L2)

[USDA.sol - Line 02](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L2)

[Vault.sol - Line 02](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L2)

[VaultController.sol - Line 02](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L2)

[VaultDeployer.sol - Line 02](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultDeployer.sol#L2)

[WUSDA.sol - Line 02](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L2)

[AmphoraProtocolToken.sol - Line 02](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L2)

[GovernorCharlie.sol - Line 03](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L3)

All others inside solidity => contracts and interfaces folders

</details>

**Recommended Mitigation Steps**

Use the stable fixed pragma version.

### [L-02] - Missing validation for `address(0)` in the constructors.

**Details**

It is recommended to check for `address(0)` when updating state variables through constructor or functions. This can save the protocol from accidental updates.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[Vault.sol - Line 67](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L67)

[Vault.sol - Line 69](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L69)

[VaultController.sol - Line 102](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L102)

[VaultController.sol - Line 108](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L108)

[CbEthEthOracle.sol - Line 31 and 32](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol#L31-L32)

[ChainlinkOracleRelay.sol - Line 45](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L45)

[StableCurveLpOracle.sol - Line 22](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L22)

[TriCrypto2Oracle.sol - Line 34 - 36](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/TriCrypto2Oracle.sol#L34-L36)

</details>

### [L-03] - Missing validation for `address(0)` in the functions changing state variables.

**Details**

It is recommended to check for `address(0)` when updating state variables through constructor or functions. This can save the protocol from accidental updates.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[USDA.sol - Line 143](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L143)

[USDA.sol - Line 225](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L225)

[USDA.sol - Line 246](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L246)

[USDA.sol - Line 279](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L279)

[VaultController.sol - Line 418](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L418)

[VaultController.sol - Line 980](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L980)

[CurveMaster.sol - Line 41](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L41)

[CurveMaster.sol - Line 52](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L52)

[UFragments.sol - Line 269](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L269)

[UFragments.sol - Line 284](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L284)

</details>

### [L-04] - Execution throw `underflow` error.

**Details**

In the `Vault` contract, inside the `withdrawERC20` function token address balance is decrementing `balances[_tokenAddress] -= _amount;` when vault owner withdraw the `sUSD` but when he will try to get more than his balance, he will get the underflow error which is not meaningful and difficult for users to understand the meaning of it.

Same inside `controllerTransfer` function.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[Vault.sol - Line 125](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L125)

[Vault.sol - Line 286](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L286)

</details>

**Recommended Mitigation Steps**

It would be better to check the withdrawal amount and if amount is greater than balance then revert a meaningful error which can be understandable by users along with error location.

### [L-05] - `ABIEncoderV2` is not experimental anymore.

**Details**

Starting from `0.8.0`, it is enabled by default.

> Because the ABI coder v2 is not considered experimental anymore, it can be selected via `pragma abicoder v2`

See the docs [ABIEncoderV2](https://docs.soliditylang.org/en/v0.8.21/layout-of-source-files.html#abiencoderv2)

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[GovernorCharlie.sol - Line 04](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L4)

[IAmphoraProtocolToken.sol - Line 03](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IAmphoraProtocolToken.sol#L3)

</details>

# Non Critical Findings

## Table Of Content

| Number                                                                                    | Issue                                                                       | Instances |
| :---------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------- | --------: |
| [N-01](#n-01---set-the-internal-layout-of-contracts-interfaces-and-libraries)             | Set the Internal Layout of Contracts, Interfaces, and Libraries             |        28 |
| [N-02](#n-02---unnecessary-conversions)                                                   | Unnecessary Conversions                                                     |         2 |
| [N-03](#n-03---avoid-magic-numbers)                                                       | Avoid magic numbers                                                         |         1 |
| [N-04](#n-04---be-consistent-with-license-in-smart-contracts)                             | Be consistent with license in smart contracts                               |         3 |
| [N-05](#n-05---use-natspec-comments-for-smart-contracts-interfaces-and-libraries)         | Use `Natspec` comments for smart contracts, interfaces and libraries        |        11 |
| [N-06](#n-06---variable-whose-value-is-already-know-before-deployment-should-be-constant) | Variable whose value is already know before deployment should be `constant` |         2 |
| [N-07](#n-07---wrong-error-meaning)                                                       | Wrong error meaning                                                         |         1 |
| [N-08](#n-08---internal-functions-and-variables-should-follow-the-convention)             | Internal functions and variables should follow the convention               |         3 |
| [N-09](#n-09---function-should-follow-the-naming-convention)                              | Function should follow the naming convention                                |        11 |
| [N-10](#n-10---use-natspec-comments-for-functions)                                        | Use `Natspec` comments for functions                                        |         8 |
| [N-11](#n-11---lack-of-indexed-events-parameters)                                         | Lack of Indexed Events Parameters                                           |         5 |
| [N-12](#n-12---fix-the-comments)                                                          | Fix the comments                                                            |         6 |

### [N-01] - Set the Internal Layout of Contracts, Interfaces, and Libraries.

**Details**

According to the Solidity [Style Guide](https://docs.soliditylang.org/en/v0.8.21/style-guide.html#style-guide) consistency in the projects layout is very important. If all projects apply the style guides provided by solidity docs then understanding the projects and its code will be much easier for developers, and auditors.

Solidity docs;

> A style guide is about consistency. Consistency with this style guide is important. Consistency within a project is more important. Consistency within one module or function is most important.

According to the solidity styles guide;

Inside each contract, library or interface, this order should be followed:

1. Type declarations
2. State variables
3. Events
4. Errors
5. Modifiers
6. Functions

And this should be the order of functions:

-   constructor
-   receive function (if exists)
-   fallback function (if exists)
-   external
-   public
-   internal
-   private
-   View and pure functions last

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[AMPHClaimer.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol)

[USDA.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol)

[Vault.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol)

[VaultController.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol)

[WUSDA.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol)

[GovernorCharlie.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol)

[CurveMaster.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol)

[CTokenOracle.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol)

[CbEthEthOracle.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol)

[ChainlinkOracleRelay.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol)

[ChainlinkTokenOracleRelay.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol)

[EthSafeStableCurveOracle.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol)

[OracleRelay.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol)

[StableCurveLpOracle.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol)

[TriCrypto2Oracle.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/TriCrypto2Oracle.sol)

[UniswapV3OracleRelay.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol)

[ThreeLines0_100.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol)

[UFragments.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol)

[IAMPHClaimer.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IAMPHClaimer.sol)

[IUSDA.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol)

[IVault.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVault.sol)

[IVaultController.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol)

[IWUSDA.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IWUSDA.sol)

[IGovernorCharlie.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IGovernorCharlie.sol)

[ICurveMaster.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICurveMaster.sol)

[IOracleRelay.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/IOracleRelay.sol)

[IBaseRewardPool.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IBaseRewardPool.sol)

[ICurvePool.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol)

[IVaultDeployer.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultDeployer.sol)

</details>

**Recommended Mitigation Steps**

Apply the recommeded layout sturcture in all mentioned contracts according to solidity styles guide.

### [N-02] - Unnecessary Conversions.

**Details**

These mentioned conversions are unnecessary, this will cost extra gas and affect the readability.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

Pauser is already addrss not need to convert.  
[USDA.sol - Line 42](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L42)

Vault is itself a contract which inherits the IVault, we do not need to cast it. We can return the Vault itself.  
[VaultDeployer.sol - Line 28](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultDeployer.sol#L28)

</details>

**Recommended Mitigation Steps**

Remove these unnecessary conversion for better readability and less cost.

### [N-03] - Avoid magic numbers.

**Details**

Magic numbers are numbers such as `365 days` or `6 hours` and these affects the readability and difficult to understand in the context. It would be much better to replace them with constant variables with meaningful names.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[VaultController.sol - Line 948](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L948)

</details>

**Recommended Mitigation Steps**

Replace them with constant variables with meaningful names.

### [N-04] - Be consistent with license in smart contracts.

**Details**

All of the smart contracts uses `MIT` license but some contracts define different licenses. Use only 1 license.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[WUSDA.sol - Line 01](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L1)

[ChainlinkStalePriceLib.sol - Line 01](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol#L1)

[IVaultDeployer.sol - Line 01](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultDeployer.sol#L1)

</details>

**Recommended Mitigation Steps**

Use only 1 license.

### [N-05] - Use `Natspec` comments for smart contracts, interfaces and libraries.

**Details**

It is recommended by solidity docs that solidity contracts should be fully annotated using NatSpec for all public interfaces (everything in the ABI).

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[AmphoraProtocolToken.sol - Line 08](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L8)

[GovernorCharlie.sol - Line 11](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L11)

[ChainlinkStalePriceLib.sol - Line 06](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol#L6)

[IVirtualBalanceRewardPool.sol - line 06](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IVirtualBalanceRewardPool.sol#L6)

[ICurvePool.sol - Line 06](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol#L6)

[ICurvePool.sol - Line 14](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol#L14)

[ICurvePool.sol - Line 26](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol#L26)

[IBooster.sol - Line 04](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IBooster.sol#L4)

[IBaseRewardPool.sol - Line 07](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IBaseRewardPool.sol#L7)

[ICToken.sol - Line 04](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICToken.sol#L4)

[IAMPH.sol - Line 04](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IAMPH.sol#L4)

</details>

**Recommended Mitigation Steps**

Add the `Natspec` comments in the above mentioned contracts.

### [N-06] - Variable whose value is already know before deployment should be `constant`.

**Details**

It is a convension to use the `constant` variables for all of the values which are already know before deployment of the contract.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[GovernorCharlie.sol - Line 90 - 102](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L90-L102)

[UFragments.sol - Line 101](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L101)

</details>

**Recommended Mitigation Steps**

Use `constant` variable for all these variables

### [N-07] - Wrong error meaning.

**Details**

In the `ChainlinkOracleRelay` contract's `setStalePriceDelay` revert the wrong meaning error `ChainlinkOracle_ZeroAmount` when the passed argument delay will be zero.

It should be `ChainlinkOracle_ZeroDelay`.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[ChainlinkOracleRelay.sol - Line 66](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L66)

</details>

**Recommended Mitigation Steps**

Repalce `ChainlinkOracle_ZeroAmount` with `ChainlinkOracle_ZeroDelay`

### [N-08] - Internal functions and variables should follow the convention.

**Details**

Underscore Prefix for Non-external Functions and Variables

-   `_singleLeadingUnderscore`

This convention is suggested for non-external functions and state variables (`private` or `internal`).

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[ChainlinkStalePriceLib.sol - Line 11](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol#L11)

[UFragments.sol - Lines 61 - 63](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L61-L63)

This is public variable and should not be prefix with `_` `uint256 public _totalGons;`.  
[UFragments.sol - Line 67](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L67)

</details>

**Recommended Mitigation Steps**

Apply the convention on these functions and variables.

### [N-09] - Function should follow the naming convention.

**Details**

Functions should use mixedCase. Examples: `getBalance`, `transfer`, `verifyOwner`, `addMember`, `changeOwner`.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[IAMPHClaimer.sol - Lines 55 to 64](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IAMPHClaimer.sol#L55-L64)

[IAMPHClaimer.sol - Line 70](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IAMPHClaimer.sol#L70)

[ICurvePool.sol - Lines 07 - 11](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol#L7-L11)

[ICurvePool.sol - Lines 15 - 20](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol#L15-L20)

[ICurvePool.sol - Lines 28 - 34](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol#L28-L34)

[ICurveAddressesProvider.sol - Line 08](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICurveAddressesProvider.sol#L8)

[ICurveAddressesProvider.sol - Line 15](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICurveAddressesProvider.sol#L15)

[IVaultController.sol - Line 263](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol#L263)

[IVaultController.sol - Line 335](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol#L335)

[IVaultController.sol - Lines 341 - 344](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol#L341-L344)

[IVault.sol - Line 150](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVault.sol#L150)

</details>

**Recommended Mitigation Steps**

Update these functions' names to `mixedCase`

### [N-10] - Use `Natspec` comments for functions.

**Details**

It is recommended by solidity docs that solidity contracts should be fully annotated using NatSpec for all public interfaces (everything in the ABI).

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[ICurvePool.sol - Lines - 07 - 11](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol#L7-L11)

[ICurvePool.sol - Lines 15 - 23](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol#L15-L23)

[ICurvePool.sol - Lines 27 - 34](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICurvePool.sol#L27-L34)

[IVirtualBalanceRewardPool.sol - Lines 07 - 10](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IVirtualBalanceRewardPool.sol#L7-L10)

[ICVX.sol - Lines 10 - 12](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/ICVX.sol#L10-L12)

[IBooster.sol - Lines 05 - 15](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IBooster.sol#L5-L15)

[IBaseRewardPool.sol - Lines 08 - 17](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/utils/IBaseRewardPool.sol#L8-L17)

[IAmphoraProtocolToken.sol - Line 21](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IAmphoraProtocolToken.sol#L21)

</details>

**Recommended Mitigation Steps**

Add the `Natspec` comments in the above mentioned functions.

### [N-11] - Lack of Indexed Events Parameters.

**Details**

To facilitate off-chain searching and filtering for specific events information try to utilize the 3 available indexed parameters for events.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[ICurveMaster.sol - Line 42](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICurveMaster.sol#L16-L42)

[IVaultController.sol - Lines 23 - 139](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol#L23-L139)

[IVault.sol - Lines 20 - 41](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVault.sol#L20-L41)

[IUSDA.sol - Lines 33 - 40](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol#L33-L40)

[IUSDA.sol - Line 68](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol#L68)

</details>

**Recommended Mitigation Steps**

Add `indexed` keyword with events parameters for useful information.

### [N-12] - Fix the comments.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[IGovernorCharlie.sol - Line 87](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IGovernorCharlie.sol#L87)

[IVaultDeployer.sol - Line 23](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultDeployer.sol#L23)

[IVaultController.sol - Line 239](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol#L239)

[IVault.sol - Line 104](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVault.sol#L104)

[IUSDA.sol - Line 108](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol#L108)

[IAMPHClaimer.sol - Line 51](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IAMPHClaimer.sol#L51)

</details>
