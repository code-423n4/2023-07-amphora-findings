---
sponsor: "Amphora Protocol"
slug: "2023-07-amphora"
date: "2024-04-22"
title: "Amphora Protocol"
findings: "https://github.com/code-423n4/2023-07-amphora-findings/issues"
contest: 266
---

# Overview

## About C4

Code4rena (C4) is an open organization consisting of security researchers, auditors, developers, and individuals with domain expertise in smart contracts.

A C4 audit is an event in which community participants, referred to as Wardens, review, audit, or analyze smart contract logic in exchange for a bounty provided by sponsoring projects.

During the audit outlined in this document, C4 conducted an analysis of the Amphora Protocol smart contract system written in Solidity. The audit took place between July 21—July 26 2023.

## Wardens

73 Wardens contributed reports to the Amphora Protocol audit:

  1. [minhtrng](https://code4rena.com/@minhtrng)
  2. [said](https://code4rena.com/@said)
  3. [0xComfyCat](https://code4rena.com/@0xComfyCat)
  4. [ljmanini](https://code4rena.com/@ljmanini)
  5. [SanketKogekar](https://code4rena.com/@SanketKogekar)
  6. [emerald7017](https://code4rena.com/@emerald7017)
  7. [ak1](https://code4rena.com/@ak1)
  8. [K42](https://code4rena.com/@K42)
  9. [0xSmartContract](https://code4rena.com/@0xSmartContract)
  10. Musaka ([0x3b](https://code4rena.com/@0x3b) and [ZdravkoHr](https://code4rena.com/@ZdravkoHr))
  11. [Limbooo](https://code4rena.com/@Limbooo)
  12. [DavidGiladi](https://code4rena.com/@DavidGiladi)
  13. [qpzm](https://code4rena.com/@qpzm)
  14. [kutugu](https://code4rena.com/@kutugu)
  15. [giovannidisiena](https://code4rena.com/@giovannidisiena)
  16. [dharma09](https://code4rena.com/@dharma09)
  17. [0xWaitress](https://code4rena.com/@0xWaitress)
  18. [Giorgio](https://code4rena.com/@Giorgio)
  19. [SpicyMeatball](https://code4rena.com/@SpicyMeatball)
  20. [0xbranded](https://code4rena.com/@0xbranded)
  21. [Bauchibred](https://code4rena.com/@Bauchibred)
  22. [hunter\_w3b](https://code4rena.com/@hunter_w3b)
  23. [alymurtazamemon](https://code4rena.com/@alymurtazamemon)
  24. [Rolezn](https://code4rena.com/@Rolezn)
  25. [erebus](https://code4rena.com/@erebus)
  26. [mert\_eren](https://code4rena.com/@mert_eren)
  27. [pep7siup](https://code4rena.com/@pep7siup)
  28. [Qeew](https://code4rena.com/@Qeew)
  29. [josephdara](https://code4rena.com/@josephdara)
  30. [adeolu](https://code4rena.com/@adeolu)
  31. [naman1778](https://code4rena.com/@naman1778)
  32. [SM3\_SS](https://code4rena.com/@SM3_SS)
  33. [SAQ](https://code4rena.com/@SAQ)
  34. [LeoS](https://code4rena.com/@LeoS)
  35. [Aymen0909](https://code4rena.com/@Aymen0909)
  36. [SY\_S](https://code4rena.com/@SY_S)
  37. [ybansal2403](https://code4rena.com/@ybansal2403)
  38. [ReyAdmirado](https://code4rena.com/@ReyAdmirado)
  39. [Raihan](https://code4rena.com/@Raihan)
  40. [excalibor](https://code4rena.com/@excalibor)
  41. [piyushshukla](https://code4rena.com/@piyushshukla)
  42. [eeshenggoh](https://code4rena.com/@eeshenggoh)
  43. [halden](https://code4rena.com/@halden)
  44. [MohammedRizwan](https://code4rena.com/@MohammedRizwan)
  45. [Kaysoft](https://code4rena.com/@Kaysoft)
  46. [sakshamguruji](https://code4rena.com/@sakshamguruji)
  47. [Arabadzhiev](https://code4rena.com/@Arabadzhiev)
  48. [Deekshith99](https://code4rena.com/@Deekshith99)
  49. [No12Samurai](https://code4rena.com/@No12Samurai)
  50. [Eeyore](https://code4rena.com/@Eeyore)
  51. [Topmark](https://code4rena.com/@Topmark)
  52. [mojito\_auditor](https://code4rena.com/@mojito_auditor)
  53. [ro1sharkm](https://code4rena.com/@ro1sharkm)
  54. [bigtone](https://code4rena.com/@bigtone)
  55. [mrpathfindr](https://code4rena.com/@mrpathfindr)
  56. [Hama](https://code4rena.com/@Hama)
  57. [0xmuxyz](https://code4rena.com/@0xmuxyz)
  58. [jeffy](https://code4rena.com/@jeffy)
  59. [deth](https://code4rena.com/@deth)
  60. [sivanesh_808](https://code4rena.com/@sivanesh_808)
  61. [SAAJ](https://code4rena.com/@SAAJ)
  62. [PNS](https://code4rena.com/@PNS)
  63. [Brenzee](https://code4rena.com/@Brenzee)
  64. [grearlake](https://code4rena.com/@grearlake)
  65. [T1MOH](https://code4rena.com/@T1MOH)
  66. [Walter](https://code4rena.com/@Walter)
  67. [btk](https://code4rena.com/@btk)
  68. [8olidity](https://code4rena.com/@8olidity)
  69. [nnez](https://code4rena.com/@nnez)
  70. [uzay](https://code4rena.com/@uzay)
  71. [debo](https://code4rena.com/@debo)
  72. [VIELITE](https://code4rena.com/@VIELITE)

This audit was judged by [LSDan](https://twitter.com/lsdan_defi).

Final report assembled by [liveactionllama](https://twitter.com/liveactionllama).

# Summary

The C4 analysis yielded an aggregated total of 6 unique vulnerabilities. Of these vulnerabilities, 3 received a risk rating in the category of HIGH severity and 3 received a risk rating in the category of MEDIUM severity.

Additionally, C4 analysis included 51 reports detailing issues with a risk rating of LOW severity or non-critical. There were also 18 reports recommending gas optimizations.

All of the issues presented here are linked back to their original finding.

# Scope

The code under review can be found within the [C4 Amphora Protocol repository](https://github.com/code-423n4/2023-07-amphora), and is composed of 49 smart contracts written in the Solidity programming language and includes 2,859 lines of Solidity code.

# Severity Criteria

C4 assesses the severity of disclosed vulnerabilities based on three primary risk categories: high, medium, and low/non-critical.

High-level considerations for vulnerabilities span the following key areas when conducting assessments:

- Malicious Input Handling
- Escalation of privileges
- Arithmetic
- Gas use

For more information regarding the severity criteria referenced throughout the submission review process, please refer to the documentation provided on [the C4 website](https://code4rena.com), specifically our section on [Severity Categorization](https://docs.code4rena.com/awarding/judging-criteria/severity-categorization).

# High Risk Findings (3)
## [[H-01] Reentrancy issue with the `withdraw` method of USDC. All tokens could be drained.](https://github.com/code-423n4/2023-07-amphora-findings/issues/304)
*Submitted by [SanketKogekar](https://github.com/code-423n4/2023-07-amphora-findings/issues/304), also found by [ak1](https://github.com/code-423n4/2023-07-amphora-findings/issues/349) and [emerald7017](https://github.com/code-423n4/2023-07-amphora-findings/issues/206)*

<https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/USDA.sol#L147-L157><br>
<https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/USDA.sol#L114>

### Impact

High: All USDC tokens could be drained from the protocol.

### Proof of Concept

There is a reentrancy issue with the 'withdraw' methods of USDC.

A user could call either of the external withdraw functions: `withdraw` or `withdrawTo`.

Which further calls `_withdraw();` which basically burns the user's token and transfers user the `_susdAmount`. The exchange is 1 to 1.

```solidity
function withdraw(uint256 _susdAmount) external override {
    _withdraw(_susdAmount, _msgSender());
}
```

```solidity
  function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
    if (reserveAmount == 0) revert USDA_EmptyReserve();
    if (_susdAmount == 0) revert USDA_ZeroAmount();
    if (_susdAmount > this.balanceOf(_msgSender())) revert USDA_InsufficientFunds();
    // Account for the susd withdrawn
    reserveAmount -= _susdAmount;
    sUSD.transfer(_target, _susdAmount);
    _burn(_msgSender(), _susdAmount);

    emit Withdraw(_target, _susdAmount);
  }
```

The issue is that the `_withdraw()` function does not follow the CEI pattern and fails to update the state (burning token in this case) before the token transfer.

```solidity
sUSD.transfer(_target   , _susdAmount);
_burn(_msgSender(), _susdAmount);
```

It is possible to re-enter the function `USDC.withdraw()` from the malicious attack contract's fallback function as soon as it recieves the transfered amount.

This will again hit the attacker contract's fallback function (with the withdrawn token amount) and repeat the flow until USDA tokens from contract are completely drained.

<https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/USDA.sol#L147-L157>

### Recommended Mitigation Steps

This could be prevented by first burning the user's tokens or using a Mutex / Re-entrancy guard from OpenZeppelin lib.

### Assessed type

Reentrancy



***

## [[H-02] crvRewardsContract `getReward` can be called directly, breaking vaults `claimRewards` functionallity](https://github.com/code-423n4/2023-07-amphora-findings/issues/301)
*Submitted by [said](https://github.com/code-423n4/2023-07-amphora-findings/issues/301), also found by [minhtrng](https://github.com/code-423n4/2023-07-amphora-findings/issues/410)*

crvRewardsContract of convex can be called by anyone on behalf of Vault, this will allow malicious users to call `getReward` and break the Vault `claimRewards` functionality.

### Proof of Concept

Convex rewarded allow anyone to call `getReward` on behalf of any users:

<https://github.com/convex-eth/platform/blob/main/contracts/contracts/BaseRewardPool.sol#L263-L279>

This will break Vault `claimRewards` functionality that depends on this `getReward`:

<https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229>

```solidity
  function claimRewards(address[] memory _tokenAddresses) external override onlyMinter {
    uint256 _totalCrvReward;
    uint256 _totalCvxReward;

    IAMPHClaimer _amphClaimer = CONTROLLER.claimerContract();
    for (uint256 _i; _i < _tokenAddresses.length;) {
      IVaultController.CollateralInfo memory _collateralInfo = CONTROLLER.tokenCollateralInfo(_tokenAddresses[_i]);
      if (_collateralInfo.tokenId == 0) revert Vault_TokenNotRegistered();
      if (_collateralInfo.collateralType != IVaultController.CollateralType.CurveLPStakedOnConvex) {
        revert Vault_TokenNotCurveLP();
      }

      IBaseRewardPool _rewardsContract = _collateralInfo.crvRewardsContract;
      uint256 _crvReward = _rewardsContract.earned(address(this));

      if (_crvReward != 0) {
        // Claim the CRV reward
        _totalCrvReward += _crvReward;
        _rewardsContract.getReward(address(this), false);
        _totalCvxReward += _calculateCVXReward(_crvReward);
      }

   ...
  }
```

This will allow malicious users to call `getReward` on behalf of Vaults, and basically prevent them to mint get the rewards and get the deserved AMPH tokens.

### Recommended Mitigation Steps

Create another functionality inside Vault that similar to `claimRewards`, but used CVX, CRV balance inside the contract, to perform the AMPH claim and claim the rewards.

**[0xShaito (Amphora) confirmed and commented](https://github.com/code-423n4/2023-07-amphora-findings/issues/301#issuecomment-1667507614):**
 > True! We will fix this.
> 
> Impact of the attack would be the loss of the rewards. No user deposits at risk.



***

## [[H-03] Rounding error in `WUSDA` can result in loss of user funds, especially when manipulated by an attacker](https://github.com/code-423n4/2023-07-amphora-findings/issues/256)
*Submitted by [giovannidisiena](https://github.com/code-423n4/2023-07-amphora-findings/issues/256), also found by [giovannidisiena](https://github.com/code-423n4/2023-07-amphora-findings/issues/274), [mert\_eren](https://github.com/code-423n4/2023-07-amphora-findings/issues/372), [pep7siup](https://github.com/code-423n4/2023-07-amphora-findings/issues/355), [Bauchibred](https://github.com/code-423n4/2023-07-amphora-findings/issues/331), qpzm ([1](https://github.com/code-423n4/2023-07-amphora-findings/issues/311), [2](https://github.com/code-423n4/2023-07-amphora-findings/issues/310), [3](https://github.com/code-423n4/2023-07-amphora-findings/issues/260)), said ([1](https://github.com/code-423n4/2023-07-amphora-findings/issues/277), [2](https://github.com/code-423n4/2023-07-amphora-findings/issues/272)), Giorgio ([1](https://github.com/code-423n4/2023-07-amphora-findings/issues/247), [2](https://github.com/code-423n4/2023-07-amphora-findings/issues/119)), [ljmanini](https://github.com/code-423n4/2023-07-amphora-findings/issues/220), [Musaka](https://github.com/code-423n4/2023-07-amphora-findings/issues/189), SpicyMeatball ([1](https://github.com/code-423n4/2023-07-amphora-findings/issues/169), [2](https://github.com/code-423n4/2023-07-amphora-findings/issues/168)), 0xbranded ([1](https://github.com/code-423n4/2023-07-amphora-findings/issues/100), [2](https://github.com/code-423n4/2023-07-amphora-findings/issues/99)), [0xComfyCat](https://github.com/code-423n4/2023-07-amphora-findings/issues/85), 0xWaitress ([1](https://github.com/code-423n4/2023-07-amphora-findings/issues/67), [2](https://github.com/code-423n4/2023-07-amphora-findings/issues/64)), [erebus](https://github.com/code-423n4/2023-07-amphora-findings/issues/34), kutugu ([1](https://github.com/code-423n4/2023-07-amphora-findings/issues/29), [2](https://github.com/code-423n4/2023-07-amphora-findings/issues/28)), [josephdara](https://github.com/code-423n4/2023-07-amphora-findings/issues/385), [adeolu](https://github.com/code-423n4/2023-07-amphora-findings/issues/363), [ak1](https://github.com/code-423n4/2023-07-amphora-findings/issues/362), and [SanketKogekar](https://github.com/code-423n4/2023-07-amphora-findings/issues/300)*

Due to floor rounding in Solidity, it is possible for rounding errors to affect functions in the `WUSDA` contract.

Specifically, when `WUSDA::_usdaToWUSDA` is invoked internally, if `_usdaAmount` is sufficiently small compared with `MAX_wUSDA_SUPPLY` (10M) and the total USDA supply then it is possible for the `_wusdaAmount` return value to round down to 0.

```solidity
function _usdaToWUSDA(uint256 _usdaAmount, uint256 _totalUsdaSupply) private pure returns (uint256 _wusdaAmount) {
    _wusdaAmount = (_usdaAmount * MAX_wUSDA_SUPPLY) / _totalUsdaSupply;
  }
```

When calling the `WUSDA::deposit/depositTo` functions, which call the internal `WUSDA::_deposit` function with this rounded `_wusdaAmount` value, the implication is that depositors could receive little/no WUSDA for their USDA. Additionally, calls to the `WUSDA::withdraw/withdrawTo` functions could be process without actually burning any WUSDA. This is clearly not desirable as USDA can be subsequently redeemed for sUSD (at the expense of the protocol reserves and its other depositors).

```solidity
function deposit(uint256 _usdaAmount) external override returns (uint256 _wusdaAmount) {
    _wusdaAmount = _usdaToWUSDA(_usdaAmount, _usdaSupply());
    _deposit(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
  }

function withdraw(uint256 _usdaAmount) external override returns (uint256 _wusdaAmount) {
    _wusdaAmount = _usdaToWUSDA(_usdaAmount, _usdaSupply());
    _withdraw(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
  }
```

Even when the USDA supply is sufficiently small to avoid this issue, an attacker could still utilize a sUSD flash loan to artificially inflate the USDA total supply which causes the down rounding, potentially front-running a victim such that their deposit is processed without minting any WUSDA and then withdrawing the inflated USDA supply to repay the flash loan.

### Proof of Concept

*normal case:*

*   Total USDA supply is 10B.
*   Alice calls `WUSDA::deposit` with `_usdaAmount` of 1e23 (10\_000 tokens).
*   `_wusdaAmount` is rounded down to 1e20/100 tokens (`1e23 * 1e25 / 1e28 = 1e20`).
*   Alice receives 100 WUSDA rather than 10\_000.
*   In reality, the total supply of sUSD is around 40M, so a more realistic scenario assuming 10M total USDA supply may be that Alice deposits 10e18 and receives 1 WUSDA or 1/10th the expected amount (`10e18 * 1e25 / 1e26 = 1e18`).

*flash loan & attacker front-running case:*

*   Total USDA supply is 1M.
*   Alice calls `WUSDA::deposit` with `_usdaAmount` of 9.9e22 (99\_000 tokens).
*   Attacker front-runs this transaction and calls `WUSDA::deposit` with `_usdaAmount` of 3.3e22 (33\_000 tokens).
*   Attacker receives 33\_000 WUSDA.
*   Attacker takes out a sUSD flash loan of 39M.
*   Attacker deposits 39M sUSD into USDA.
*   Alice's transaction is included here, but `_wusdaAmount` is rounded down to 24\_750 tokens (`9.9e22 * 1e25 / 4e25 = 2.475e22`).
*   Alice receives 24\_750 WUSDA rather than 99\_000 (only 1/4 of the expected amount).
*   Attacker backruns Alice's transaction and calls `WUSDA::withdraw` with `_usdaAmount` of 132e21 (132\_000 tokens).
*   Using the same rounding logic, `_wusdaAmount` is rounded down to 33\_000 tokens (`132e21 * 1e25 / 4e25 = 33e22`).
*   Attacker withdraws 132\_000 WUSDA and receives the 99\_000 USDA deposited by Alice in addition to their original 33\_000.
*   Attacker redeems USDA, repays the flash loan and profits 90\_000 USDA/sUSD at the expense of Alice.

### Recommended Mitigation Steps

Consider reverting if either `WUSDA::_usdaToWUSDA` or `WUSDA::_wUSDAToUSDA` return a zero value. Also consider multiplication by some scalar precision amount. Protections against manipulation of USDA total supply within a single block would also be desirable, perhaps achievable by implementing some multi-block delay.

**[0xShaito (Amphora) confirmed](https://github.com/code-423n4/2023-07-amphora-findings/issues/256#issuecomment-1683501662)**



***

 
# Medium Risk Findings (3)
## [[M-01] `Vault.claimRewards` can break if Convex changes the operator](https://github.com/code-423n4/2023-07-amphora-findings/issues/414)
*Submitted by [minhtrng](https://github.com/code-423n4/2023-07-amphora-findings/issues/414)*

`Vault.claimRewards` assumes that CVX will always get minted to the Vault (if there is a CRV reward). If CVX does not get minted, the claim will fail, preventing payout of CRV and extra rewards. Looking at the Convex code there can be the case where the operator has changed, causing no mint to happen.

### Proof of Concept

The function `Vault.claimRewards` calculates the CVX reward that is expected, which is then meant to be transferred to AMPHClaimer and the minter:

```js
...
 _totalCvxReward += _calculateCVXReward(_crvReward);
...
// Claim AMPH tokens depending on how much CRV and CVX was claimed
_amphClaimer.claimAmph(this.id(), _totalCvxReward, _totalCrvReward, _msgSender());
...
if (_totalCvxReward > 0) CVX.transfer(_msgSender(), _totalCvxReward);
```

The `Cvx` contract which performs the mint contains the following logic:

```js
function mint(address _to, uint256 _amount) external {
    if(msg.sender != operator){
        //dont error just return. if a shutdown happens, rewards on old system
        //can still be claimed, just wont mint cvx
        return;
    }
```

The `operator` is the `Booster` contract which is called by `BaseRewardPool` which again is called by `Vault.claimRewards`. As can be seen in the snippet, the Convex protocol enables a shutdown case that wont break the `BaseRewardPool`, however the `Vault` implementation has not taken that case into consideration.

Severity deemed to be medium, due to high impact but special requirement.

### Recommended Mitigation Steps

Check the CVX balance of the vault before and after the claim to assert that the correct CVX amount has been minted.

**[0xShaito (Amphora) confirmed and commented](https://github.com/code-423n4/2023-07-amphora-findings/issues/414#issuecomment-1667512003):**
 > Impact: Users wouldn't be able to claim rewards anymore and accumulated rewards are lost. No user deposits at risk!



***

## [[M-02] Reorg attack on user's Vault deployment and deposit may lead to theft of funds](https://github.com/code-423n4/2023-07-amphora-findings/issues/233)
*Submitted by [ljmanini](https://github.com/code-423n4/2023-07-amphora-findings/issues/233)*

`VaultDeployer` deploys instances of the `Vault` contract using `create`, where the address derivation depends on the sender's address (= `VaultDeployer`) and its nonce.

Although the system is intended to be deployed on Ethereum, there still have been instances of chain reorgs in its PoS era : [ref](https://decrypt.co/101390/ethereum-beacon-chain-blockchain-reorg).

The frequency and depth of reorgs only increases in alternative L1s and L2s such as polygon and optimism, where reorgs have reached 100+ block depth : [ref](https://protos.com/polygon-hit-by-157-block-reorg-despite-hard-fork-to-reduce-reorgs/).

### Proof of Concept

In a scenario in which a user's `Vault` deplyoment and deposit is included fully within a reorg, an attacker is able to frontrun the user's `Vault` deployment, effectively stealing the `Vault` instance at that particular address, include the user's deployment (which will deploy a new `Vault` at a new, unrelated address) and his deposit to the **originally deployed `Vault`**, and finally backrun the user's deposit by pulling funds from the `Vault`.

In order to pull off this attack successfully, the attacker needs to take care of some details:

1.  When frontrunning the `Vault` deployment, he must call `VaultDeployer.deployVault()` through a malicious contract under his control which implements the minimal interface to allow calls the `Vault` makes to the `VaultController` to not revert. Notice that this malicious `VaultController` could allow only deposits to go through in the `Vault`, but disallow withdrawals by returning `false` when `Vault` attempts to call `CONTROLLER.checkVault` within `Vault.withdrawERC20`.
2.  When frontrunning the `Vault` deployment, the attacker must set the `Vault`'s minter to the victim's address, in order for the deposit to successfully go through within the `Vault`.

Once the victim's deposit has been executed, the attacker can chose to pull the user's funds from the compromised `Vault` by calling `Vault.controllerTransfer` through his malicious `VaultController`

Given the high impact of this finding but its low likelihood, I'm submitting this as a medium severity issue.

### Recommended Mitigation Steps

Deployments of `Vault` instances should be done employing `create2` with a `salt` calculated based on `id`, `minter` and `msg.sender`.



***

## [[M-03] When Convex pool is shut down while collateral type is `CurveLPStakedOnConvex`, users unable to deposit that asset and protocol lose the ability to accept the asset as collateral further ](https://github.com/code-423n4/2023-07-amphora-findings/issues/90)
*Submitted by [0xComfyCat](https://github.com/code-423n4/2023-07-amphora-findings/issues/90)*

<https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L376-L412><br>
<https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L93-L105><br>
<https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L111-L133><br>
<https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L676-L679>

When Convex pool of an asset that has `CurveLPStakedOnConvex` collateral type is shut down, users can no longer deposit that asset into vault since it would always revert on Convex booster `deposit` function, called during vault’s `depositERC20` process. With lack of method to update it, protocol would the ability to accept it as collateral even as plain LP without staking. Also users that use the asset as collateral may face risk of being liquidated.
This is also applicable when Convex itself shutdown their booster contract.

### Proof of Concept

Based on project's e2e test, extending `CommonE2EBase` contract. The test showed that `depositERC20` is bricked when Convex pool is shut down.

```
import {CommonE2EBase} from '@test/e2e/Common.sol';

import {IERC20} from 'isolmate/interfaces/tokens/IERC20.sol';
import {IVault} from '@interfaces/core/IVault.sol';

interface IBoosterAdmin {
  function poolManager() external view returns (address);
  function shutdownPool(uint256 _pid) external;
}

contract VaultCollateralTypeVulnPoC is CommonE2EBase {
  function setUp() public override {
    super.setUp();
  }

  function testCannotDepositWhenConvexPoolIsShutDown() public {
    // Prepare Convex LP for user
    deal(USDT_LP_ADDRESS, bob, 2 ether);

    // User mints vault
    IVault bobVault = IVault(vaultController.vaultIdVaultAddress(_mintVault(bob)));

    // User deposit Convex LP to vault
    vm.startPrank(bob);
    IERC20(USDT_LP_ADDRESS).approve(address(bobVault), 1 ether);
    bobVault.depositERC20(USDT_LP_ADDRESS, 1 ether);
    vm.stopPrank();

    // Convex pool of the asset is shut down
    vm.prank(IBoosterAdmin(address(BOOSTER)).poolManager());
    IBoosterAdmin(address(BOOSTER)).shutdownPool(1);

    // User can no longer deposit that LP to vault
    vm.startPrank(bob);
    IERC20(USDT_LP_ADDRESS).approve(address(bobVault), 1 ether);
    vm.expectRevert('pool is closed');
    bobVault.depositERC20(USDT_LP_ADDRESS, 1 ether);
    vm.stopPrank();
  }
}

```

### Tools Used

Foundry

### Recommended Mitigation Steps

The fix requires 2 modifications

1.  Allow updating collateral type to `Single` and pool id to 0 when pool or booster contract is shutting down. By updating collateral type to `Single` during `depositERC20` deposit to Convex would be skipped. Updating pool id to 0 prevents users from staking it manually.

```

      function updateRegisteredErc20(
        address _tokenAddress,
        uint256 _ltv,
        address _oracleAddress,
        uint256 _liquidationIncentive,
        uint256 _cap,
        uint256 _poolId,
        bool _shutdown
      ) external override onlyOwner {
        ...
        if (_poolId != 0) {
          (address _lpToken,,, address _crvRewards,,) = BOOSTER.poolInfo(_poolId);
          if (_lpToken != _tokenAddress) revert VaultController_TokenAddressDoesNotMatchLpAddress();
          _collateral.collateralType = CollateralType.CurveLPStakedOnConvex;
          _collateral.crvRewardsContract = IBaseRewardPool(_crvRewards);
          _collateral.poolId = _poolId;
        } else if (_shutdown) {
          (,,,,,bool _isPoolShutdown) = BOOSTER.poolInfo(_poolId);
          if (!_isPoolShutdown || !BOOSTER.isShutdown()) revert VaultController_NotShutdown();
          _collateral.collateralType = CollateralType.Single;
          // keep `crvRewardsContract` for withdrawal
          _collateral.poolId = 0;
        }
        ...
      }
```

2.  Update vault's `withdrawERC20` to update state variable `isTokenStaked` to false when withdraw all of that asset. This is important because if the asset is deposited again without actually being staked but `isTokenStaked` is still true, the vault will not be liquidated due to trying to withdraw while nothing is staked.

Failed liquidation if state is not reflected correctly

      function liquidateVault(
        ...
      ) external override paysInterest whenNotPaused returns (uint256 _toLiquidate) {
        ...
        // withdraw from convex
        CollateralInfo memory _assetInfo = tokenAddressCollateralInfo[_assetAddress];
        // @audit if the flag is not updated accordingly this would fail
        if (_vault.isTokenStaked(_assetAddress)) {
          _vault.controllerWithdrawAndUnwrap(_assetInfo.crvRewardsContract, _tokensToLiquidate);
        }
        ...
      }

The fix

      function withdrawERC20(address _tokenAddress, uint256 _amount) external override onlyMinter {
        ...
        if (isTokenStaked[_tokenAddress]) {
          if (!CONTROLLER.tokenCrvRewardsContract(_tokenAddress).withdrawAndUnwrap(_amount, false)) {
            revert Vault_WithdrawAndUnstakeOnConvexFailed();
            // @audit if withdraw all, reset flag to false
          } else if (_amount == balances[_tokenAddress]) {
            isTokenStaked[_tokenAddress] = false;
          }
        }
        ...
      }

**[0xShaito (Amphora) disagreed with severity and commented](https://github.com/code-423n4/2023-07-amphora-findings/issues/90#issuecomment-1667510422):**
 > While true, this needs the action of a third party (convex) and is not an attack that someone can do. We consider this to be of medium severity.
> 
> User deposits are also safe in case this happens but the collateral gets bricked for new deposits. 

**[LSDan (judge) decreased severity to Medium and commented](https://github.com/code-423n4/2023-07-amphora-findings/issues/90#issuecomment-1677286454):**
 > I agree with the sponsor here. Medium is more appropriate. There are clear external factors.

**[0xShaito (Amphora) confirmed](https://github.com/code-423n4/2023-07-amphora-findings/issues/90#issuecomment-1679065063)**



***

# Low Risk and Non-Critical Issues

For this audit, 51 reports were submitted by wardens detailing low risk and non-critical issues. The [report highlighted below](https://github.com/code-423n4/2023-07-amphora-findings/issues/382) by **DavidGiladi** received the top score from the judge.

*The following wardens also submitted reports: [0xSmartContract](https://github.com/code-423n4/2023-07-amphora-findings/issues/428), [Bauchibred](https://github.com/code-423n4/2023-07-amphora-findings/issues/394), [Musaka](https://github.com/code-423n4/2023-07-amphora-findings/issues/326), [hunter\_w3b](https://github.com/code-423n4/2023-07-amphora-findings/issues/324), [kutugu](https://github.com/code-423n4/2023-07-amphora-findings/issues/285), [alymurtazamemon](https://github.com/code-423n4/2023-07-amphora-findings/issues/271), [Rolezn](https://github.com/code-423n4/2023-07-amphora-findings/issues/49), [Qeew](https://github.com/code-423n4/2023-07-amphora-findings/issues/430), [halden](https://github.com/code-423n4/2023-07-amphora-findings/issues/420), [MohammedRizwan](https://github.com/code-423n4/2023-07-amphora-findings/issues/417), [naman1778](https://github.com/code-423n4/2023-07-amphora-findings/issues/402), [Kaysoft](https://github.com/code-423n4/2023-07-amphora-findings/issues/401), [qpzm](https://github.com/code-423n4/2023-07-amphora-findings/issues/390), [Limbooo](https://github.com/code-423n4/2023-07-amphora-findings/issues/388), [sakshamguruji](https://github.com/code-423n4/2023-07-amphora-findings/issues/373), [Arabadzhiev](https://github.com/code-423n4/2023-07-amphora-findings/issues/369), [SanketKogekar](https://github.com/code-423n4/2023-07-amphora-findings/issues/347), [Deekshith99](https://github.com/code-423n4/2023-07-amphora-findings/issues/346), [No12Samurai](https://github.com/code-423n4/2023-07-amphora-findings/issues/333), [Eeyore](https://github.com/code-423n4/2023-07-amphora-findings/issues/315), [Topmark](https://github.com/code-423n4/2023-07-amphora-findings/issues/313), [ljmanini](https://github.com/code-423n4/2023-07-amphora-findings/issues/296), [mojito\_auditor](https://github.com/code-423n4/2023-07-amphora-findings/issues/295), [ro1sharkm](https://github.com/code-423n4/2023-07-amphora-findings/issues/270), [josephdara](https://github.com/code-423n4/2023-07-amphora-findings/issues/264), [SM3\_SS](https://github.com/code-423n4/2023-07-amphora-findings/issues/251), [bigtone](https://github.com/code-423n4/2023-07-amphora-findings/issues/246), [erebus](https://github.com/code-423n4/2023-07-amphora-findings/issues/231), [mrpathfindr](https://github.com/code-423n4/2023-07-amphora-findings/issues/225), [giovannidisiena](https://github.com/code-423n4/2023-07-amphora-findings/issues/219), [Hama](https://github.com/code-423n4/2023-07-amphora-findings/issues/190), [0xmuxyz](https://github.com/code-423n4/2023-07-amphora-findings/issues/176), [jeffy](https://github.com/code-423n4/2023-07-amphora-findings/issues/174), [deth](https://github.com/code-423n4/2023-07-amphora-findings/issues/167), [sivanesh\_808](https://github.com/code-423n4/2023-07-amphora-findings/issues/160), [SAAJ](https://github.com/code-423n4/2023-07-amphora-findings/issues/159), [PNS](https://github.com/code-423n4/2023-07-amphora-findings/issues/158), [Brenzee](https://github.com/code-423n4/2023-07-amphora-findings/issues/157), [grearlake](https://github.com/code-423n4/2023-07-amphora-findings/issues/149), [T1MOH](https://github.com/code-423n4/2023-07-amphora-findings/issues/144), [Walter](https://github.com/code-423n4/2023-07-amphora-findings/issues/140), [btk](https://github.com/code-423n4/2023-07-amphora-findings/issues/138), [8olidity](https://github.com/code-423n4/2023-07-amphora-findings/issues/129), [0xComfyCat](https://github.com/code-423n4/2023-07-amphora-findings/issues/115), [adeolu](https://github.com/code-423n4/2023-07-amphora-findings/issues/112), [nnez](https://github.com/code-423n4/2023-07-amphora-findings/issues/75), [uzay](https://github.com/code-423n4/2023-07-amphora-findings/issues/47), [debo](https://github.com/code-423n4/2023-07-amphora-findings/issues/35), [VIELITE](https://github.com/code-423n4/2023-07-amphora-findings/issues/12), and [0xWaitress](https://github.com/code-423n4/2023-07-amphora-findings/issues/2).*

## Summary

### Low Issues
|Issue|Instances|
|-|:-:|
|[L-01] Burn functions must be protected with a modifier | 4 |
|[L-02] Divide before multiply | 2 |
|[L-03] ERC20 Approve Call is Not Safe | 3 |
|[L-04] Fee/Rate Caps Missing in Smart Contracts | 2 |
|[L-05] Reentrancy vulnerabilities | 14 |
|[L-06] Arrays can grow without a way to shrink them | 1 |
|[L-07] Zero-value transfers may revert | 5 |

Total: 7 issues

### Non-Critical Issues
|Issue|Instances|
|-|:-:|
|[N-01] Do not calculate constants | 7 |
|[N-02] Cyclomatic complexity | 1 |
|[N-03] Dead-code | 1 |
|[N-04] Else block not required | 7 |
|[N-05] Hardcoded addresses in contract | 3 |
|[N-06] Interfaces Should Be Defined in Separate Files From Their Usage | 1 |
|[N-07] Token contract should have a blacklist function or modifier | 4 |
|[N-08] Missing inheritance | 3 |
|[N-09] Redundant Inheritance | 1 |

Total: 9 issues

## [L-01] Burn functions must be protected with a modifier
- Severity: Low
- Confidence: High

### Description
If burn functions are not protected by a modifier, any address may be able to burn tokens, potentially leading to financial loss. A common modifier to use is `onlyOwner`.

<details>

<summary>
There are 4 instances of this issue:

</summary>

###
- File: solidity/contracts/core/USDA.sol
```
 
Line: 188          function _burn(address _target, uint256 _amount) internal 
```
Burn function without a protective modifier.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L188-L198](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L188-L198)

- File: solidity/contracts/core/WUSDA.sol
```
 
Line: 79          function burn(uint256 _wusdaAmount) external override returns (uint256 _usdaAmount) 
```
Burn function without a protective modifier.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L79-L82](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L79-L82)

- File: solidity/interfaces/core/IUSDA.sol
```
 
Line: 194          function burn(uint256 _susdAmount) external;
```
Burn function without a protective modifier.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol#L194](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol#L194)

- File: solidity/interfaces/core/IWUSDA.sol
```
 
Line: 78          function burn(uint256 _wusdaAmount) external returns (uint256 _usdaAmount);
```
Burn function without a protective modifier.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IWUSDA.sol#L78](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IWUSDA.sol#L78)

</details>

## [L-02] Divide before multiply
- Severity: Low
- Confidence: Medium

### Description
Solidity's integer division truncates. Thus, performing division before multiplication can lead to precision loss.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- File: solidity/contracts/core/AMPHClaimer.sol
```
 
Line: 202          function _calculate(uint256 _tokenAmountToSend) internal view returns (uint256 _amphAmount) 
```

performs a multiplication on the result of a division:<br>
- File: solidity/contracts/core/AMPHClaimer.sol

```
 
Line: 222          _amphForThisTurn = ((_rate * _tempAmountReceived) / 1e12) / 1e6
```

- File: solidity/contracts/core/AMPHClaimer.sol
```
 
Line: 241          _amphToMint += (_amphForThisTurn * 1e12)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L202-L246](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L202-L246)

- File: solidity/contracts/utils/ThreeLines0_100.sol
```
 
Line: 76          function _linearInterpolation(
    int256 _rise,
    int256 _run,
    int256 _distance,
    int256 _b
  ) private pure returns (int256 _result) 
```

performs a multiplication on the result of a division:<br>
- File: solidity/contracts/utils/ThreeLines0_100.sol
```
 
Line: 83          int256 _mE6 = (_rise * 1e6) / _run
```

- File: solidity/contracts/utils/ThreeLines0_100.sol
```
 
Line: 86          _result = (_mE6 * _distance) / 1e6 + _b
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol#L76-L88](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol#L76-L88)

</details>

## [L-03] ERC20 Approve Call is Not Safe
- Severity: Low
- Confidence: High

### Description
This detector identifies instances where the approve() method is used on ERC20 tokens. The approve() function can be vulnerable to a specific attack, where a malicious actor can double spend tokens. This scenario occurs when the owner changes the approved allowance from N to M (N>0, M>0). The malicious spender can observe this change and quickly transfer the original N tokens before the change is mined, and then spend the additional M tokens afterwards. This could result in a total transfer of N+M tokens, which is not what the owner intended. More info you can see in this [link](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

For this reason, it's recommended to use `safeIncreaseAllowance()` or `safeDecreaseAllowance()` for better control. If these methods are not available, using `safeApprove()` is also an option as it reverts if the current approval is not zero, providing an additional layer of security.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
- File: solidity/contracts/core/Vault.sol
```
 
Line: 212          CRV.approve(address(_amphClaimer), _takenCRV)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L212](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L212)

- File: solidity/contracts/core/Vault.sol
```
 
Line: 213          CVX.approve(address(_amphClaimer), _takenCVX)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L213](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L213)

- File: solidity/contracts/core/Vault.sol
```
 
Line: 321          IERC20(_token).approve(address(_booster), _amount)
```
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L321](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L321)

</details>

## [L-04] Fee/Rate Caps Missing in Smart Contracts
- Severity: Low
- Confidence: High

### Description
Fees/rates charged by contracts should be capped at a certain limit, preferably below 100%, to ensure users are not exploited. This detector checks for the absence of such limitations. The presence of fees/rates without upper limits could pose financial risks to the users of the contract.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- File: solidity/contracts/core/AMPHClaimer.sol
```
 
Line: 137          cvxRewardFee = _newFee
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L137](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L137)

- File: solidity/contracts/core/AMPHClaimer.sol
```
 
Line: 145          crvRewardFee = _newFee
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L145](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L145)

</details>

## [L-05] Reentrancy vulnerabilities
- Severity: Low
- Confidence: Medium

### Description

Detection of the [reentrancy bug](https://github.com/trailofbits/not-so-smart-contracts/tree/master/reentrancy).<br>
Only report reentrancy that acts as a double call (see `reentrancy-eth`, `reentrancy-no-eth`).

<details>

<summary>
There are 14 instances of this issue:

</summary>

###
- Reentrancy in File: solidity/contracts/core/USDA.sol
```
 
Line: 101          function _deposit(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused 
```

External calls:<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 103          sUSD.transferFrom(_msgSender(), address(this), _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 101          paysInterest
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 49          IVaultController(_vaultControllers.at(_i)).calculateInterest()
```

State variables written after the call(s):<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 104          _mint(_target, _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 170          _gonBalances[_target] += _amount * __gonsPerFragment
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 104          _mint(_target, _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 174          _totalGons += _amount * __gonsPerFragment
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 104          _mint(_target, _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 172          _totalSupply += _amount
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 106          reserveAmount += _susdAmount
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L101-L109](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L101-L109)

- Reentrancy in File: solidity/contracts/core/USDA.sol
```
 
Line: 147          function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused 
```

External calls:<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 147          paysInterest
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 49          IVaultController(_vaultControllers.at(_i)).calculateInterest()
```

State variables written after the call(s):<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 152          reserveAmount -= _susdAmount
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L147-L157](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L147-L157)

- Reentrancy in File: solidity/contracts/core/USDA.sol
```
 
Line: 147          function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused 
```

External calls:<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 153          sUSD.transfer(_target, _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 147          paysInterest
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 49          IVaultController(_vaultControllers.at(_i)).calculateInterest()
```

State variables written after the call(s):<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 154          _burn(_msgSender(), _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 191          _gonBalances[_target] -= (_amount * __gonsPerFragment)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 154          _burn(_msgSender(), _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 194          _totalGons -= (_amount * __gonsPerFragment)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 154          _burn(_msgSender(), _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 193          _totalSupply -= _amount
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L147-L157](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L147-L157)

- Reentrancy in File: solidity/contracts/core/USDA.sol
```
 
Line: 182          function burn(uint256 _susdAmount) external override paysInterest onlyOwner 
```

External calls:<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 182          paysInterest
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 49          IVaultController(_vaultControllers.at(_i)).calculateInterest()
```

State variables written after the call(s):<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 184          _burn(_msgSender(), _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 191          _gonBalances[_target] -= (_amount * __gonsPerFragment)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 184          _burn(_msgSender(), _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 194          _totalGons -= (_amount * __gonsPerFragment)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 184          _burn(_msgSender(), _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 193          _totalSupply -= _amount
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L182-L185](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L182-L185)

- Reentrancy in File: solidity/contracts/core/USDA.sol
```
 
Line: 202          function donate(uint256 _susdAmount) external override paysInterest whenNotPaused 
```

External calls:<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 202          paysInterest
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 49          IVaultController(_vaultControllers.at(_i)).calculateInterest()
```

State variables written after the call(s):<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 205          reserveAmount += _susdAmount
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L202-L208](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L202-L208)

- Reentrancy in File: solidity/contracts/core/USDA.sol
```
 
Line: 202          function donate(uint256 _susdAmount) external override paysInterest whenNotPaused 
```

External calls:<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 206          sUSD.transferFrom(_msgSender(), address(this), _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 202          paysInterest
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 49          IVaultController(_vaultControllers.at(_i)).calculateInterest()
```

State variables written after the call(s):<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 207          _donation(_susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 262          _gonsPerFragment = _totalGons / _totalSupply
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 207          _donation(_susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 260          _totalSupply += _amount
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 261          _totalSupply = MAX_SUPPLY
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L202-L208](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L202-L208)

- Reentrancy in File: solidity/contracts/core/USDA.sol
```
 
Line: 161          function mint(uint256 _susdAmount) external override paysInterest onlyOwner 
```

External calls:<br>
- File: solidity/contracts/core/USDA.sol
```
 
Line: 161          paysInterest
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 49          IVaultController(_vaultControllers.at(_i)).calculateInterest()
```

State variables written after the call(s):
- File: solidity/contracts/core/USDA.sol
```
 
Line: 163          _mint(_msgSender(), _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 170          _gonBalances[_target] += _amount * __gonsPerFragment
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 163          _mint(_msgSender(), _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 174          _totalGons += _amount * __gonsPerFragment
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 163          _mint(_msgSender(), _susdAmount)
```

- File: solidity/contracts/core/USDA.sol
```
 
Line: 172          _totalSupply += _amount
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L161-L164](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L161-L164)

- Reentrancy in File: solidity/contracts/core/Vault.sol
```
 
Line: 284          function controllerTransfer(address _token, address _to, uint256 _amount) external override onlyVaultController 
```

External calls:<br>
- File: solidity/contracts/core/Vault.sol
```
 
Line: 285          SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_token), _to, _amount)
```

State variables written after the call(s):<br>
- File: solidity/contracts/core/Vault.sol
```
 
Line: 286          balances[_token] -= _amount
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L284-L287](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L284-L287)

- Reentrancy in File: solidity/contracts/core/Vault.sol
```
 
Line: 89          function depositERC20(address _token, uint256 _amount) external override onlyMinter 
```

External calls:<br>
- File: solidity/contracts/core/Vault.sol
```
 
Line: 92          SafeERC20Upgradeable.safeTransferFrom(IERC20Upgradeable(_token), _msgSender(), address(this), _amount)
```

State variables written after the call(s):<br>
- File: solidity/contracts/core/Vault.sol
```
 
Line: 102          isTokenStaked[_token] = true
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L89-L109](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L89-L109)

- Reentrancy in File: solidity/contracts/core/Vault.sol
```
 
Line: 117          function withdrawERC20(address _tokenAddress, uint256 _amount) external override onlyMinter 
```

External calls:<br>
- File: solidity/contracts/core/Vault.sol
```
 
Line: 120          !CONTROLLER.tokenCrvRewardsContract(_tokenAddress).withdrawAndUnwrap(_amount, false)
```

State variables written after the call(s):<br>
- File: solidity/contracts/core/Vault.sol
```
 
Line: 125          balances[_tokenAddress] -= _amount
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L117-L133](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L117-L133)

- Reentrancy in File: solidity/contracts/core/VaultController.sol
```
 
Line: 278          function mintVault() public override whenNotPaused returns (address _vaultAddress) 
```

External calls:<br>
- File: solidity/contracts/core/VaultController.sol
```
 
Line: 282          _vaultAddress = _createVault(vaultsMinted, _msgSender())
```

- File: solidity/contracts/core/VaultController.sol
```
 
Line: 980          _vault = address(VAULT_DEPLOYER.deployVault(_id, _minter))
```

State variables written after the call(s):<br>
- File: solidity/contracts/core/VaultController.sol
```
 
Line: 284          vaultIdVaultAddress[vaultsMinted] = _vaultAddress
```

- File: solidity/contracts/core/VaultController.sol
```
 
Line: 287          walletVaultIDs[_msgSender()].push(vaultsMinted)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L278-L291](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L278-L291)

- Reentrancy in File: solidity/contracts/periphery/CurveMaster.sol
```
 
Line: 41          function setCurve(address _tokenAddress, address _curveAddress) external override onlyOwner 
```

External calls:<br>
- File: solidity/contracts/periphery/CurveMaster.sol
```
 
Line: 42          IVaultController(vaultControllerAddress).calculateInterest()
```

State variables written after the call(s):<br>
- File: solidity/contracts/periphery/CurveMaster.sol
```
 
Line: 44          curves[_tokenAddress] = _curveAddress
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L41-L47](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L41-L47)

- Reentrancy in File: solidity/contracts/periphery/oracles/CbEthEthOracle.sol
```
 
Line: 70          function _updateVirtualPrice() internal 
```

External calls:<br>
- File: solidity/contracts/periphery/oracles/CbEthEthOracle.sol
```
 
Line: 74          CB_ETH_POOL.claim_admin_fees()
```

State variables written after the call(s):<br>
- File: solidity/contracts/periphery/oracles/CbEthEthOracle.sol
```
 
Line: 76          virtualPrice = _virtualPrice
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol#L70-L77](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol#L70-L77)

- Reentrancy in File: solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol
```
 
Line: 36          function _updateVirtualPrice() internal 
```

External calls:<br>
- File: solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol
```
 
Line: 41          CRV_POOL.remove_liquidity(0, _amounts)
```

State variables written after the call(s):<br>
- File: solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol
```
 
Line: 43          virtualPrice = _virtualPrice
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol#L36-L44](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol#L36-L44)

</details>

## [L-06] Arrays can grow without a way to shrink them
- Severity: Low
- Confidence: High

### Description
It's a good practice to maintain control over the size of array state variables in Solidity, especially if they are dynamically updated. If a contract includes a mechanism to push items into an array, it should ideally also provide a mechanism to remove items. Ignoring this can lead to bloated and inefficient contracts.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###

Line: 3

- File: solidity/contracts/periphery/oracles/StableCurveLpOracle.sol
```
 
Line: 17          IOracleRelay[] public anchoredUnderlyingTokens
```
Array grow:<br>File: solidity/contracts/periphery/oracles/StableCurveLpOracle.sol
```
 
Line: 23          anchoredUnderlyingTokens.push(_anchoredUnderlyingTokens[_i])
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L17](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L17)

</details>

## [L-07] Zero-value transfers may revert
- Severity: Low
- Confidence: High

### Description
This detector identifies instances where zero-valued ERC20 token transfers are made. Even though EIP-20 states that zero-valued transfers must be treated as normal transfers and events must be triggered, some tokens may revert if this is attempted, which can cause transactions that involve other tokens (such as batch operations) to fully revert. Therefore, it may be safer and more gas-efficient to skip the transfer if the amount is zero.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- File: solidity/contracts/core/AMPHClaimer.sol
```
 
Line: 90          CVX.safeTransferFrom(msg.sender, owner(), _cvxAmountToSend)
```
This variable might be zero: `_cvxAmountToSend` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L90](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L90)

- File: solidity/contracts/core/USDA.sol
```
 
Line: 216          sUSD.transfer(_to, _amount)
```
This variable might be zero: `_amount` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L216](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L216)

- File: solidity/contracts/core/USDA.sol
```
 
Line: 246          sUSD.transfer(_target, _susdAmount)
```
This variable might be zero: `_susdAmount` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L246](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L246)

- File: solidity/contracts/core/WUSDA.sol
```
 
Line: 215          IERC20(USDA).safeTransferFrom(_from, address(this), _usdaAmount)
```
This variable might be zero: `_usdaAmount` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L215](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L215)

- File: solidity/contracts/core/WUSDA.sol
```
 
Line: 228          IERC20(USDA).safeTransfer(_to, _usdaAmount)
```
This variable might be zero: `_usdaAmount` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L228](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L228)

</details>

## [N-01] Do not calculate constants
- Severity: Non-Critical
- Confidence: High

### Description
Due to how constant variables are implemented in Solidity (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas. While in most cases, the compiler will optimize these computations away, it is considered a best practice to write code that does not rely on the compiler optimization.

<details>

<summary>
There are 7 instances of this issue:

</summary>

###
- File: solidity/contracts/core/AMPHClaimer.sol
```
 
Line: 18          uint256 internal constant _FIFTY_MILLIONS = 50_000_000 * 1e6
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L18](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L18)

- File: solidity/contracts/core/AMPHClaimer.sol
```
 
Line: 21          uint256 internal constant _TWENTY_FIVE_THOUSANDS = 25_000 * 1e6
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L21](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L21)

- File: solidity/contracts/core/AMPHClaimer.sol
```
 
Line: 24          uint256 internal constant _FIFTY = 50 * 1e6
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L24](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L24)

- File: solidity/contracts/core/AMPHClaimer.sol
```
 
Line: 27          uint256 public constant BASE_SUPPLY_PER_CLIFF = 8_000_000 * 1e6
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L27](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L27)

- File: solidity/contracts/core/WUSDA.sol
```
 
Line: 35          uint256 public constant MAX_wUSDA_SUPPLY = 10_000_000 * (10 ** 18)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L35](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L35)

- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 62          uint256 private constant MAX_UINT256 = 2 ** 256 - 1
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L62](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L62)

- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 63          uint256 private constant INITIAL_FRAGMENTS_SUPPLY = 1 * 10 ** DECIMALS
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L63](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L63)

</details>

## [N-02] Cyclomatic complexity
- Severity: Non-Critical
- Confidence: High

### Description
Detects functions with high (> 11) cyclomatic complexity.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- File: solidity/contracts/core/Vault.sol
```
 
Line: 164          function claimRewards(address[] memory _tokenAddresses) external override onlyMinter 
```
 has a high cyclomatic complexity (12).

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229)

</details>

## [N-03] Dead-code
- Severity: Non-Critical
- Confidence: Medium

### Description
Functions that are not used.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- File: solidity/contracts/periphery/oracles/StableCurveLpOracle.sol
```
 
Line: 57          function _getVirtualPrice() internal view virtual returns (uint256 _value) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L57-L59](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L57-L59)

</details>

## [N-04] Else block not required
- Severity: Non-Critical
- Confidence: High

### Description
This detector identifies unnecessary else blocks in the code. When an if-statement block ends with a return statement, any subsequent else block becomes superfluous and can be eliminated to reduce code complexity. 

Note that this check also applies to single-line else conditions. For instance, in 'return a > b ? a : b', the else condition is not needed.

<details>

<summary>
There are 7 instances of this issue:

</summary>

###
- File: solidity/contracts/core/AMPHClaimer.sol
```
 
Line: 182          if (_amphBalance >= _amphByCrv) {
      // contract has the full amount
      _cvxAmountToSend = _cvxRewardsFeeToExchange;
      _crvAmountToSend = _crvRewardsFeeToExchange;
      _claimableAmph = _amphByCrv;
    } else {
      // contract doesnt have the full amount
      return (0, 0, 0);
    }
```
No need to declare else block.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L182-L190](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L182-L190)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 475          if (_proposal.executed) return ProposalState.Executed;
    else if (block.timestamp >= (_proposal.eta + GRACE_PERIOD)) return ProposalState.Expired
```
No need to declare else block.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L475-L476](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L475-L476)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 474          if (_proposal.eta == 0) return ProposalState.Succeeded;
    else if (_proposal.executed) return ProposalState.Executed;
    else if (block.timestamp >= (_proposal.eta + GRACE_PERIOD)) return ProposalState.Expired
```
No need to declare else block.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L474-L476](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L474-L476)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 469          if (
      (_whitelisted && _proposal.againstVotes > _proposal.quorumVotes)
        || (!_whitelisted && _proposal.forVotes <= _proposal.againstVotes)
        || (!_whitelisted && _proposal.forVotes < _proposal.quorumVotes)
    ) return ProposalState.Defeated;
    else if (_proposal.eta == 0) return ProposalState.Succeeded;
    else if (_proposal.executed) return ProposalState.Executed;
    else if (block.timestamp >= (_proposal.eta + GRACE_PERIOD)) return ProposalState.Expired
```
No need to declare else block.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L469-L476](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L469-L476)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 468          if (block.number <= _proposal.endBlock) return ProposalState.Active;
    else if (
      (_whitelisted && _proposal.againstVotes > _proposal.quorumVotes)
        || (!_whitelisted && _proposal.forVotes <= _proposal.againstVotes)
        || (!_whitelisted && _proposal.forVotes < _proposal.quorumVotes)
    ) return ProposalState.Defeated;
    else if (_proposal.eta == 0) return ProposalState.Succeeded;
    else if (_proposal.executed) return ProposalState.Executed;
    else if (block.timestamp >= (_proposal.eta + GRACE_PERIOD)) return ProposalState.Expired
```
No need to declare else block.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L468-L476](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L468-L476)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 467          if (block.number <= _proposal.startBlock) return ProposalState.Pending;
    else if (block.number <= _proposal.endBlock) return ProposalState.Active;
    else if (
      (_whitelisted && _proposal.againstVotes > _proposal.quorumVotes)
        || (!_whitelisted && _proposal.forVotes <= _proposal.againstVotes)
        || (!_whitelisted && _proposal.forVotes < _proposal.quorumVotes)
    ) return ProposalState.Defeated;
    else if (_proposal.eta == 0) return ProposalState.Succeeded;
    else if (_proposal.executed) return ProposalState.Executed;
    else if (block.timestamp >= (_proposal.eta + GRACE_PERIOD)) return ProposalState.Expired
```
No need to declare else block.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L467-L476](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L467-L476)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 466          if (_proposal.canceled) return ProposalState.Canceled;
    else if (block.number <= _proposal.startBlock) return ProposalState.Pending;
    else if (block.number <= _proposal.endBlock) return ProposalState.Active;
    else if (
      (_whitelisted && _proposal.againstVotes > _proposal.quorumVotes)
        || (!_whitelisted && _proposal.forVotes <= _proposal.againstVotes)
        || (!_whitelisted && _proposal.forVotes < _proposal.quorumVotes)
    ) return ProposalState.Defeated;
    else if (_proposal.eta == 0) return ProposalState.Succeeded;
    else if (_proposal.executed) return ProposalState.Executed;
    else if (block.timestamp >= (_proposal.eta + GRACE_PERIOD)) return ProposalState.Expired
```
No need to declare else block.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L466-L476](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L466-L476)

</details>

## [N-05] Hardcoded addresses in contract
- Severity: Non-Critical
- Confidence: High

### Description
Addresses should not be hardcoded in contracts. Instead, they should be declared as immutable and assigned via constructor arguments. This practice keeps the code consistent across deployments on different networks and prevents the need for recompilation when addresses change.

<details>

<summary>
There are 3 instances of this issue:

</summary>

- File: solidity/contracts/periphery/oracles/CurveRegistryUtils.sol
```
 
Line: 8          ICurveAddressesProvider public constant CURVE_ADDRESSES_PROVIDER =
    ICurveAddressesProvider(0x0000000022D53366457F9d5E68Ec105046FC4383)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol#L8-L9](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol#L8-L9)

- File: solidity/contracts/periphery/oracles/OracleRelay.sol
```
 
Line: 8          address public constant wETH_ADDRESS = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L8](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L8)

- File: solidity/contracts/periphery/oracles/WstEthOracle.sol
```
 
Line: 10          IWStETH public constant WSTETH = IWStETH(0x7f39C581F595B53c5cb19bD0b3f8dA6c935E2Ca0)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/WstEthOracle.sol#L10](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/WstEthOracle.sol#L10)

</details>

## [N-06] Interfaces Should Be Defined in Separate Files From Their Usage
- Severity: Non-Critical
- Confidence: High

### Description
Interfaces should be defined in separate files, so that it's easier for future projects to import them, and to avoid duplication later on if they need to be used elsewhere in the project.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- File: solidity/interfaces/core/IVaultController.sol
```
 
Line: 13          interface IVaultController 
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol#L13-L577](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol#L13-L577)

</details>

## [N-07] Token contract should have a blacklist function or modifier
- Severity: Non-Critical
- Confidence: Medium

### Description
NFT thefts have increased recently, so with the addition of stolen NFTs to the platform, NFTs can be converted into liquidity. To prevent this, we recommend adding a blacklist function.

<details>

<summary>
There are 4 instances of this issue:

</summary>

###
- File: solidity/contracts/core/USDA.sol
```
 
Line: 18          contract USDA is Pausable, UFragments, IUSDA, ExponentialNoError, Roles 
```
Token contract does not contain a blacklist. Consider adding one for increased security.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L18-L302](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L18-L302)

- File: solidity/contracts/core/WUSDA.sol
```
 
Line: 28          contract WUSDA is IWUSDA, ERC20, ERC20Permit 
```
Token contract does not contain a blacklist. Consider adding one for increased security.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L28-L257](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L28-L257)

- File: solidity/contracts/governance/AmphoraProtocolToken.sol
```
 
Line: 8          contract AmphoraProtocolToken is IAmphoraProtocolToken, ERC20VotesComp, Ownable 
```
Token contract does not contain a blacklist. Consider adding one for increased security.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L8-L23](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L8-L23)

- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 20          contract UFragments is Ownable, IERC20Metadata 
```
Token contract does not contain a blacklist. Consider adding one for increased security.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L20-L341](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L20-L341)

</details>

## [N-08] Missing inheritance
- Severity: Non-Critical
- Confidence: High

### Description
This detector identifies instances where a contract defines functions that match an interface's signature, but does not explicitly inherit from the interface. Explicitly declaring inheritance relationships can improve code readability and maintainability, and ensures that the contract adheres to the defined interface.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
- File: solidity/contracts/governance/AmphoraProtocolToken.sol
```
 
Line: 8          contract AmphoraProtocolToken is IAmphoraProtocolToken, ERC20VotesComp, Ownable 
```
 should inherit from File: solidity/interfaces/governance/IAMPH.sol
```
 
Line: 4          interface IAMPH 
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L8-L23](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L8-L23)

- File: solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol
```
 
Line: 11          contract ChainlinkOracleRelay is OracleRelay, Ownable 
```
 should inherit from File: solidity/interfaces/periphery/IChainlinkOracleRelay.sol
```
 
Line: 4          interface IChainlinkOracleRelay 
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L11-L77](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L11-L77)

- File: solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol
```
 
Line: 10          contract ChainlinkTokenOracleRelay is OracleRelay 
```
 should inherit from File: solidity/interfaces/periphery/IChainlinkOracleRelay.sol
```
 
Line: 4          interface IChainlinkOracleRelay 
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L10-L53](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L10-L53)

</details>

## [N-09] Redundant Inheritance
- Severity: Non-Critical
- Confidence: High

### Description
Detects when a contract inherits from another contract that it already indirectly inherits from.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- File: solidity/contracts/core/WUSDA.sol
```
 
Line: 28          contract WUSDA is IWUSDA, ERC20, ERC20Permit 
```
 The contract `WUSDA` has redundant inheritance: `ERC20` is already inherited indirectly.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L28-L257](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L28-L257)

</details>

**[0xShaito (Amphora) confirmed](https://github.com/code-423n4/2023-07-amphora-findings/issues/382#issuecomment-1667529356)**



***

# Gas Optimizations

For this audit, 18 reports were submitted by wardens detailing gas optimizations. The [report highlighted below](https://github.com/code-423n4/2023-07-amphora-findings/issues/284) by **DavidGiladi** received the top score from the judge.

*The following wardens also submitted reports: [K42](https://github.com/code-423n4/2023-07-amphora-findings/issues/181), [dharma09](https://github.com/code-423n4/2023-07-amphora-findings/issues/81), [SAQ](https://github.com/code-423n4/2023-07-amphora-findings/issues/433), [SM3\_SS](https://github.com/code-423n4/2023-07-amphora-findings/issues/418), [LeoS](https://github.com/code-423n4/2023-07-amphora-findings/issues/416), [alymurtazamemon](https://github.com/code-423n4/2023-07-amphora-findings/issues/412), [hunter\_w3b](https://github.com/code-423n4/2023-07-amphora-findings/issues/406), [Aymen0909](https://github.com/code-423n4/2023-07-amphora-findings/issues/404), [naman1778](https://github.com/code-423n4/2023-07-amphora-findings/issues/397), [SY\_S](https://github.com/code-423n4/2023-07-amphora-findings/issues/393), [ybansal2403](https://github.com/code-423n4/2023-07-amphora-findings/issues/356), [ReyAdmirado](https://github.com/code-423n4/2023-07-amphora-findings/issues/306), [Raihan](https://github.com/code-423n4/2023-07-amphora-findings/issues/172), [excalibor](https://github.com/code-423n4/2023-07-amphora-findings/issues/146), [piyushshukla](https://github.com/code-423n4/2023-07-amphora-findings/issues/55), [Rolezn](https://github.com/code-423n4/2023-07-amphora-findings/issues/50), and [eeshenggoh](https://github.com/code-423n4/2023-07-amphora-findings/issues/41).*

## Summary
|Title|Instances|Total Gas Saved|
|-|:-:|:-:|
|[G-01] Inefficient use of `abi.encode()` | 7 | 700 |
|[G-02] Multiplication and Division by 2 Should use in Bit Shifting | 2 | 40 |
|[G-03] Avoid unnecessary storage updates | 21 | 16800 |
|[G-04] Optimal State Variable Order | 1 | 5000 |
|[G-05] Check Arguments Early | 10 | - |
|[G-06] Cache External Calls in Loops | 2 | 200 |
|[G-07] Greater or Equal Comparison Costs Less Gas than Greater Comparison | 3 | 9 |
|[G-08] Using Storage Instead of Memory for structs/arrays Saves Gas | 5 | 21000 |
|[G-09] Short-circuit rules can be used to optimize some gas usage | 1 | 2100 |
|[G-10] Unnecessary Casting of Variables | 5 | - |
|[G-11] Division operations between unsigned could be unchecked | 5 | 425 |

Total: 10 issues

## [G-01] Inefficient use of `abi.encode()`
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 700

### Description
The `abi.encode()` function is less gas efficient than `abi.encodePacked()`, especially when encoding only static types. This detector identifies instances where `abi.encode()` is used and all arguments are static, suggesting that `abi.encodePacked()` could potentially be used instead for better gas efficiency. Note: `abi.encodePacked()` should not be used if any of the arguments are dynamic types, as it could lead to hash collisions.

<details>

<summary>
There are 7 instances of this issue:

</summary>

###
- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 261          queuedTransactions[keccak256(
          abi.encode(
            _proposal.targets[_i], _proposal.values[_i], _proposal.signatures[_i], _proposal.calldatas[_i], _eta
          )
        )]
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L261-L265](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L261-L265)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 299          _txHash = keccak256(abi.encode(_target, _value, _signature, _data, _eta))
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L299](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L299)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 405          bytes32 _txHash = keccak256(abi.encode(_target, _value, _signature, _data, _eta))
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L405](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L405)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 508          bytes32 _domainSeparator =
      keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(NAME)), _getChainIdInternal(), address(this)))
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L508-L509](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L508-L509)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 510          bytes32 _structHash = keccak256(abi.encode(BALLOT_TYPEHASH, _proposalId, _support))
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L510](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L510)

- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 168          return keccak256(
      abi.encode(EIP712_DOMAIN, keccak256(bytes(name)), keccak256(bytes(EIP712_REVISION)), _chainId, address(this))
    )
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L168-L170](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L168-L170)

- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 327          bytes32 _permitDataDigest = keccak256(abi.encode(PERMIT_TYPEHASH, _owner, _spender, _value, _ownerNonce, _deadline))
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L327](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L327)

</details>

## [G-02] Multiplication and Division by 2 Should use in Bit Shifting
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 40

### Description
The expressions `x * 2` and `x / 2` can be optimized for gas efficiency by utilizing bitwise operations. In Solidity, you can achieve the same results by using bitwise left shift (x << 1) for multiplication and bitwise right shift (x >> 1) for division.

Using bitwise shift operations (SHL and SHR) instead of multiplication (MUL) and division (DIV) opcodes can lead to significant gas savings. The MUL and DIV opcodes cost 5 gas, while the SHL and SHR opcodes incur a lower cost of only 3 gas.

By leveraging these more efficient bitwise operations, you can reduce the gas consumption of your smart contracts and enhance their overall performance.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- File: solidity/contracts/periphery/oracles/CbEthEthOracle.sol
```
 
Line: 63          _maxPrice = (2 * _vp * FixedPointMathLib.sqrt(_basePrices)) / 1 ether
```
 instead `2` use bit shifting `1` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol#L63](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol#L63)

- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 63          uint256 private constant INITIAL_FRAGMENTS_SUPPLY = 1 * 10 ** DECIMALS
```
 instead `1` use bit shifting `0` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L63](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L63)

</details>

## [G-03] Avoid unnecessary storage updates
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 16800

### Description
Avoid updating storage when the value hasn't changed. If the old value is equal to the new value, not re-storing the value will avoid a `SSTORE` operation (costing 2900 gas), potentially at the expense of a `SLOAD` operation (2100 gas) or a `WARMACCESS` operation (100 gas).

<details>

<summary>
There are 21 instances of this issue:

</summary>

###
- The function `setCurve()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/periphery/CurveMaster.sol
```
 
Line: 44          curves[_tokenAddress] = _curveAddress
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L44](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L44)

- The function `setDelay()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 585          proposalTimelockDelay = _proposalTimelockDelay
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L585](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L585)

- The function `setEmergencyDelay()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 596          emergencyTimelockDelay = _emergencyTimelockDelay
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L596](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L596)

- The function `setEmergencyQuorumVotes()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 662          emergencyQuorumVotes = _newEmergencyQuorumVotes
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L662](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L662)

- The function `setEmergencyVotingPeriod()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 629          emergencyVotingPeriod = _newEmergencyVotingPeriod
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L629](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L629)

- The function `setMaxWhitelistPeriod()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 576          maxWhitelistPeriod = _second
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L576](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L576)

- The function `setMonetaryPolicy()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/utils/UFragments.sol
```
 
Line: 112          monetaryPolicy = _monetaryPolicy
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L112](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L112)

- The function `setNewToken()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 568          amph = IAMPH(_token)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L568](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L568)

- The function `setOptimisticDelay()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 697          optimisticVotingDelay = _newOptimisticVotingDelay
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L697](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L697)

- The function `setOptimisticQuorumVotes()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 708          optimisticQuorumVotes = _newOptimisticQuorumVotes
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L708](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L708)

- The function `setPauser()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/core/USDA.sol
```
 
Line: 64          pauser = _pauser
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L64](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L64)

- The function `setProposalThreshold()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 640          proposalThreshold = _newProposalThreshold
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L640](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L640)

- The function `setQuorumVotes()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 651          quorumVotes = _newQuorumVotes
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L651](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L651)

- The function `setVaultController()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/periphery/CurveMaster.sol
```
 
Line: 33          vaultControllerAddress = _vaultMasterAddress
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L33](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L33)

- The function `setVotingDelay()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 607          votingDelay = _newVotingDelay
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L607](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L607)

- The function `setVotingPeriod()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 618          votingPeriod = _newVotingPeriod
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L618](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L618)

- The function `setWhitelistAccountExpiration()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 675          whitelistAccountExpirations[_account] = _expiration
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L675](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L675)

- The function `setWhitelistGuardian()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 686          whitelistGuardian = _account
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L686](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L686)

- The function `updateRegisteredErc20()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/core/VaultController.sol
```
 
Line: 399          _collateral.crvRewardsContract = IBaseRewardPool(_crvRewards)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L399](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L399)

- The function `updateRegisteredErc20()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/core/VaultController.sol
```
 
Line: 409          _collateral.cap = _cap
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L409](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L409)

- The function `updateRegisteredErc20()` changes the state variable without first verifying if the values are different.

File: solidity/contracts/core/VaultController.sol
```
 
Line: 403          _collateral.oracle = IOracleRelay(_oracleAddress)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L403](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L403)

</details>

## [G-04] Optimal State Variable Order
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 5000

### Description
Detects optimal variable order in contract storage layouts to decrease the number of storage slots used. Each storage slot used can cost at least 5,000 gas for each write operation, and potentially up to 20,000 gas if you're turning a zero value into a non-zero value. Hence, optimizing storage usage can result in significant gas savings. The real-world savings could vary depending on your contract's specific logic and the state of the Ethereum network.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- Optimization opportunity in storage variable layout of contract File: solidity/contracts/utils/UFragments.sol
```
 
Line: 20          contract UFragments is Ownable, IERC20Metadata 
```

- original variable order (count: 17 slots)
    - address monetaryPolicy
    - uint256 DECIMALS
    - uint256 MAX_UINT256
    - uint256 INITIAL_FRAGMENTS_SUPPLY
    - uint256 _totalGons
    - uint256 MAX_SUPPLY
    - uint256 _totalSupply
    - uint256 _gonsPerFragment
    - mapping(address => uint256) _gonBalances
    - string name
    - string symbol
    - uint8 decimals
    - mapping(address => mapping(address => uint256)) _allowedFragments
    - string EIP712_REVISION
    - bytes32 EIP712_DOMAIN
    - bytes32 PERMIT_TYPEHASH
    - mapping(address => uint256) _nonces


 - optimized variable order (count: 16 slots)
    - address monetaryPolicy
    - uint8 decimals
    - uint256 DECIMALS
    - uint256 MAX_UINT256
    - uint256 INITIAL_FRAGMENTS_SUPPLY
    - uint256 _totalGons
    - uint256 MAX_SUPPLY
    - uint256 _totalSupply
    - uint256 _gonsPerFragment
    - mapping(address => uint256) _gonBalances
    - string name
    - string symbol
    - mapping(address => mapping(address => uint256)) _allowedFragments
    - string EIP712_REVISION
    - bytes32 EIP712_DOMAIN
    - bytes32 PERMIT_TYPEHASH
    - mapping(address => uint256) _nonces

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L20-L341](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L20-L341)

</details>

## [G-05] Check Arguments Early
- Severity: Gas Optimization
- Confidence: High

### Description
Checks that require() or revert() statements that check input arguments are at the top of the function. Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a gas load in a function that may ultimately revert in the unhappy case.

<details>

<summary>
There are 10 instances of this issue:

</summary>

###
- File: solidity/contracts/core/Vault.sol
```
 
Line: 142          balances[_tokenAddress] == 0
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L142](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L142)

- File: solidity/contracts/core/Vault.sol
```
 
Line: 143          isTokenStaked[_tokenAddress]
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L143](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L143)

- File: solidity/contracts/core/Vault.sol
```
 
Line: 322          !_booster.deposit(_poolId, _amount, true)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L322](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L322)

- File: solidity/contracts/core/VaultController.sol
```
 
Line: 355          _ltv >= (EXP_SCALE - _liquidationIncentive)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L355](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L355)

- File: solidity/contracts/core/VaultController.sol
```
 
Line: 394          _ltv >= (EXP_SCALE - _liquidationIncentive)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L394](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L394)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 339          _getBlockTimestamp() < _eta
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L339](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L339)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 340          _getBlockTimestamp() > _eta + GRACE_PERIOD
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L340](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L340)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 514          uint256(_s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L514](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L514)

- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 330          uint256(_s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L330](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L330)

- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 334          _owner == address(0x0)
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L334](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L334)

</details>

## [G-06] Cache External Calls in Loops
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 200

### Description
This detector identifies instances of repeated external calls within loops. By moving the first external call outside of the loop and using low-level calls inside the loop, it is possible to save gas by avoiding repeated EXTCODESIZE opcodes, as there is no need to check again if the contract exists. 

Note: This detector only triggers if the function call does not return any value.

Prior to 0.8.10, the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent Solidity versions the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low-level calls never check for contract existence. 

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- File: solidity/contracts/core/Vault.sol
```
 
Line: 193          _virtualReward.getReward()
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L193](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L193)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 314          this.executeTransaction{value: _proposal.values[_i]}(
        _proposal.targets[_i], _proposal.values[_i], _proposal.signatures[_i], _proposal.calldatas[_i], _proposal.eta
      )
```

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L314-L316](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L314-L316)

</details>

## [G-07] Greater or Equal Comparison Costs Less Gas than Greater Comparison
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 9

### Description
In Solidity, the >= operator costs less gas than the > operator. This is because the Solidity compiler uses the LT opcode for >= comparisons, which requires less gas than the combination of GT and ISZERO opcodes used for > comparisons. Therefore, replacing > with >= when possible can reduce the gas cost of your contract.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
- File: solidity/contracts/core/USDA.sol
```
 
Line: 132          _susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance
```
using GREATER\_EQUAL/LESS\_EQUAL in this case is identical therefore should be use.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L132](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L132)

- File: solidity/contracts/core/USDA.sol
```
 
Line: 142          _susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance
```
 using GREATER\_EQUAL/LESS\_EQUAL in this case is identical therefore should be use.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L142](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L142)

- File: solidity/contracts/core/VaultController.sol
```
 
Line: 236          _end = _enabledTokensLength < _end ? _enabledTokensLength : _end
```
 using GREATER\_EQUAL/LESS\_EQUAL in this case is identical therefore should be use.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L236](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L236)

</details>

## [G-08] Using Storage Instead of Memory for structs/arrays Saves Gas
- Severity: Gas Optimization
- Confidence: Medium
- Total Gas Saved: 37800

### Note 
I reported issue that was overlooked by the winning bot.

### Description
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct / array to be read from storage. This incurs a Gcoldsload (2100 gas) for each field of the struct / array. If the fields are read from the new memory variable, they incur an additional MLOAD, which is more expensive than a simple stack read.

Instead of declaring the variable with the memory keyword, a more cost-effective approach is to declare the variable with the storage keyword. In this case, you can cache any fields that need to be re-read in stack variables. This approach significantly reduces gas costs, as you only incur the Gcoldsload for the fields actually read.

The only scenario where it makes sense to read the whole struct / array into a memory variable is if the full struct / array is being returned by the function or passed to a function that requires memory. Additionally, if the array / struct is being read from another memory array / struct, using a memory variable may be appropriate.

By carefully considering the storage and memory usage in your contract, you can optimize gas costs and improve the efficiency of your smart contract operations.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- File: solidity/contracts/core/Vault.sol
```
 
Line: 170          IVaultController.CollateralInfo memory _collateralInfo = CONTROLLER.tokenCollateralInfo(_tokenAddresses[_i])
```
 should be declared as `storage` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L170](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L170)

- File: solidity/contracts/core/VaultController.sol
```
 
Line: 729          CollateralInfo memory _collateral = tokenAddressCollateralInfo[_assetAddress]
```
 should be declared as `storage` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L729](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L729)

- File: solidity/contracts/core/VaultController.sol
```
 
Line: 751          CollateralInfo memory _collateral = tokenAddressCollateralInfo[_assetAddress]
```
 should be declared as `storage` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L751](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L751)

- File: solidity/contracts/core/VaultController.sol
```
 
Line: 996          uint256[] memory _tokenBalances = new uint256[](enabledTokens.length)
```
 should be declared as `storage` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L996](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L996)

- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 187          Proposal memory _newProposal = Proposal({
      id: proposalCount,
      proposer: msg.sender,
      eta: 0,
      targets: _targets,
      values: _values,
      signatures: _signatures,
      calldatas: _calldatas,
      startBlock: block.number + votingDelay,
      endBlock: block.number + votingDelay + votingPeriod,
      forVotes: 0,
      againstVotes: 0,
      abstainVotes: 0,
      canceled: false,
      executed: false,
      emergency: _emergency,
      quorumVotes: quorumVotes,
      delay: proposalTimelockDelay
    })
```
 should be declared as `storage` 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L187-L205](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L187-L205)

</details>

## [G-09] Short-circuit rules can be used to optimize some gas usage
- Severity: Gas Optimization
- Confidence: Medium
- Total Gas Saved: 2100

### Note 
I reported issue that was overlooked by the winning bot.

### Description
Some conditions may be reordered to save an SLOAD (2100 gas), as we avoid reading state variables when the first part of the condition fails (with &&), or succeeds (with ||). For instance, consider a scenario where you have a `stateVariable` (a variable stored in contract storage) and a `localVariable` (a variable in memory). 

If you have a condition like `stateVariable > 0 && localVariable > 0`, if `localVariable > 0` is false, the Solidity runtime will still execute `stateVariable > 0`, which costs an SLOAD operation (2100 gas). However, if you reorder the condition to `localVariable > 0 && stateVariable > 0`, the `stateVariable > 0` check won't happen if `localVariable > 0` is false, saving you the SLOAD gas cost.

Similarly, for the `||` operator, if you have a condition like `stateVariable > 0 || localVariable > 0`, and `stateVariable > 0` is true, the Solidity runtime will still execute `localVariable > 0`. But if you reorder the condition to `localVariable > 0 || stateVariable > 0`, and `localVariable > 0` is true, the `stateVariable > 0` check won't happen, again saving you the SLOAD gas cost.

This detector checks for such conditions in the contract and reports if any condition could be optimized by taking advantage of the short-circuiting behavior of `&&` and `||`.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- File: solidity/contracts/core/Vault.sol
```
 
Line: 158          _poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]
```

```
 // @audit: Switch ! isTokenStaked[_token] && _poolId != 0 && balances[_token] != 0 
```
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L158](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L158)

</details>

## [G-10] Unnecessary Casting of Variables
- Severity: Gas Optimization
- Confidence: High

### Description
This detector scans for instances where a variable is casted to its own type. This is unnecessary and can be safely removed to improve code readability.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- File: solidity/contracts/core/USDA.sol
```
 
Line: 42          _msgSender() != address(pauser)
```
Unnecessary cast: `address(pauser)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L42](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L42)

- File: solidity/contracts/core/VaultController.sol
```
 
Line: 946          uint192 _e18FactorIncrease = _safeu192(
      _truncate(
        _truncate((uint256(_timeDifference) * uint256(1e18) * uint256(_curveVal)) / (365 days + 6 hours))
          * uint256(interest.factor)
      )
    )
```
Unnecessary cast: `uint256(1e18)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L946-L951](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L946-L951)

- File: solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol
```
 
Line: 75          _value = (uint256(_latest) * MULTIPLY) / DIVIDE
```
Unnecessary cast: `uint256(_latest)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L75](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L75)

- File: solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol
```
 
Line: 24          AGGREGATOR = ChainlinkOracleRelay(_feedAddress)
```
Unnecessary cast: `ChainlinkOracleRelay(_feedAddress)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L24](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L24)

- File: solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol
```
 
Line: 25          BASE_AGGREGATOR = ChainlinkOracleRelay(_baseFeedAddress)
```
Unnecessary cast: `ChainlinkOracleRelay(_baseFeedAddress)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L25](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L25)

</details>

## [G-11] Division operations between unsigned could be unchecked
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 425

### Description
Division operations on unsigned integers should be unchecked to save gas since they cannot overflow or underflow. Because unsigned integers cannot have negative values, execution of division operations outside `unchecked` blocks adds nothing but overhead. Saves about 85 gas.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- File: solidity/contracts/core/AMPHClaimer.sol
```
 
Line: 250          _cliff = _distributedAmph / BASE_SUPPLY_PER_CLIFF
```
Should be unchecked - \_distributedAmph / BASE\_SUPPLY\_PER\_CLIFF.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L250](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L250)

- File: solidity/contracts/core/USDA.sol
```
 
Line: 262          _gonsPerFragment = _totalGons / _totalSupply
```
Should be unchecked - \_totalGons / \_totalSupply.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L262](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L262)

- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 104          _gonsPerFragment = _totalGons / _totalSupply
```
Should be unchecked - \_totalGons / \_totalSupply.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L104](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L104)

- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 196          uint256 _value = _gonValue / _gonsPerFragment
```
Should be unchecked - \_gonValue / \_gonsPerFragment.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L196](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L196)

- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 245          uint256 _value = _gonValue / _gonsPerFragment
```
Should be unchecked - \_gonValue / \_gonsPerFragment.<br>
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L245](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L245)

</details>

**[0xShaito (Amphora) confirmed](https://github.com/code-423n4/2023-07-amphora-findings/issues/284#issuecomment-1679071625)**



***


# Audit Analysis

For this audit, 7 analysis reports were submitted by wardens. An analysis report examines the codebase as a whole, providing observations and advice on such topics as architecture, mechanism, or approach. The [report highlighted below](https://github.com/code-423n4/2023-07-amphora-findings/issues/381) by **0xSmartContract** received the top score from the judge.

*The following wardens also submitted reports: [Limbooo](https://github.com/code-423n4/2023-07-amphora-findings/issues/389), [Musaka](https://github.com/code-423n4/2023-07-amphora-findings/issues/323), [K42](https://github.com/code-423n4/2023-07-amphora-findings/issues/265), [Qeew](https://github.com/code-423n4/2023-07-amphora-findings/issues/407), [0xComfyCat](https://github.com/code-423n4/2023-07-amphora-findings/issues/370), and [emerald7017](https://github.com/code-423n4/2023-07-amphora-findings/issues/223).*

## Summary
| List |Head |Details|
|:--|:----------------|:------|
| a |The approach I followed when reviewing the code | Stages in my code review and analysis |
| b |Analysis of the code base | What is unique? How are the existing patterns used? |
| c |Test analysis | Test scope of the project and quality of tests |
| d |Architectural | Architecture feedback |
| e |Documents  | What is the scope and quality of documentation for Users and Administrators? |
| f |Centralization risks | How was the risk of centralization handled in the project, what could be alternatives? |
| g |Systemic risks | Potential systemic risks in the project |
| h |Competition analysis| What are similar projects? |
| i |Security Approach of the Project | Audit approach of the Project |
| j |Other Audit Reports and Automated Findings | What are the previous Audit reports and their analysis |
| k |Gas Optimization | Gas usage approach of the project and alternative solutions to it |
| l |New insights and learning from this audit | Things learned from the project |

### a) The approach I followed when reviewing the code

First, by examining the scope of the code, I determined my code review and analysis strategy.<br>
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

### b) Analysis of the code base

**VaultController.sol**<br>
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

**GovernorCharlie.sol**<br>
The GovernorCharlie contract for the Amphora protocol. GovernorCharlie is a governance contract that allows Amphora users to propose and vote on changes to the protocol.

The contract is based on the Compound Governor contract, and it implements a number of features that allow for secure and democratic governance. These features include:

- Proposal voting: Users can propose changes to the protocol, and other users can vote on these proposals.
- Quorum: A proposal must meet a certain quorum of votes before it can be passed.
- Voting weight: The voting weight of each user is based on the amount of USDA they hold.
- Treasury: The GovernorCharlie contract controls the Amphora treasury, and it can be used to fund proposals that are passed by the community.
The GovernorCharlie contract is an important part of the Amphora protocol, and it ensures that the protocol is governed in a democratic and transparent way.

**Vault.sol**<br>
The Vault contract is a core component of the Amphora protocol. It allows users to open and manage vaults, which are used to borrow and lend sUSD. Each vault is unique to a user, and it can hold both ERC-20 tokens and Curve LP tokens.

**USDA.sol**<br>
The USDA contract is a core component of the Amphora protocol. It is a rebase token that automatically rebases collecting yield. The contract is based on the OpenZeppelin ERC20Rebase contract, and it implements a number of features that allow for secure and transparent rebase mechanics.

### c) Test analysis
What did the project do differently?<br>
The project has written an effective invariant test suite.<br>
Invariant tests; Rather than describing properties of specific functions, it defines "immutable properties" about a particular contract or system of contracts that should always apply. These are things like "this vault contract always holds enough coins to cover all withdrawals", "`x*y` in a Uniswap pool always equals k" or "the total supply of this ERC20 token always equals the sum of all individual balances" it could be.

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

What could they have done better?<br>
There are many unit tests in the project, integration tests in which the interaction of contracts with each other are modeled should be increased.

### d) Architectural 
Amphora's core is based on a fork of the "Interest Protocol" by Gfx Labs.<br>
The basis of the architecture is based on the "Capital Efficiency of Interest Protocol";

https://interestprotocol.io/book/docs/concepts/Borrowing/CapitalEfficiency/

**Interest Rates**

All USDi borrowers pay a single borrowing interest rate that varies depending on the current reserve ratio. The interest paid by borrowers, net of a protocol fee, is distributed pro rata to all USDi holders.

**Reserve Ratio**

The *reserve ratio* is defined as

$$
\frac{\text{USDC in protocol reserve}}{\text{USDi total supply}}
$$

The reserve ratio measures the amount of USDC liquidity that the protocol currently has to meet the potential redemption demand of USDi holders.

**Interest Rate**
The borrow APR is a decreasing function of the reserve ratio and is defined as follows:

The function has two kinks, $(s_1,r_1)$ and $(s_2,r_2)$. The interval $[s_1,s_2]$ is the range of reserve ratios that the protocol targets. The current parameters are provided in the table below.

| $s_1$ | $s_2$ |    | $r_1$ | $r_2$ | $r_3$|
|-------|-------|----|-------|-------|------|
| 60%   | 40%   |    | 0.5%  | 10%   | 600% |

![image](https://github.com/code-423n4/2023-07-amphora/assets/104318932/cd01673a-8580-4633-a418-7f256ea9b097)

### e) Documents 
Documentation should be increased further, it is recommended to add the architectural design to the documents as infographic

### f) Centralization risks 
There is a risk of centrality in the project as the onlyOwner.<br>
The owner role has a single point of failure and onlyOwner can use critical functions, posing a centralization issue. There is always a chance for owner keys to be stolen, and in such a case, the attacker can cause serious damage to the project due to important functions.

The code detail of this topic is specified in automatic finding;<br>
https://gist.github.com/thebrittfactor/0cfcacbd1366b927c268d0c41b86781b#m-07-centralization-issue-caused-by-admin-privileges  

### g) Systemic risks 
The most important system risk in the project; Risks arising from the use of Oracle, the project uses Chainlink, Curve and Uniswap V3 oracles, security exploits from oracles have been increasing recently, so using Oracle is a risk in itself and it is important to minimize this risk.

### h) Competition analysis
Similar protocols ; Interest Protocol, Convex and Synthetix.<br>
Amphora's core is based on a fork of the "Interest Protocol" by Gfx Labs. They use a math model called 3 lines to manage interest rates. Robust documentation can be found here: https://interestprotocol.io/book/docs/concepts/Borrowing/CapitalEfficiency/ and here https://interestprotocol.io/book/docs/concepts/Borrowing/InterestRates/

### i) Security Approach of the Project
Successful current security understanding of the project;<br>
1. They manage the 2nd audit process with an innovative audit such as Code4rena, in which many auditors examine the codes.

What the project should add in the understanding of Security;<br>
1. By distributing the project to testnets, ensuring that the audits are carried out in onchain audit. (This will increase coverage)
2. After the project is published on the mainnet, there should be emergency action plans (not found in the documents)
3. After the inspection, the concept of "continuous inspection" should be adhered to with bug bounty programs such as ImmuneFi.

### j) Other Audit Reports and Automated Findings 
**Automated Findings:**<br>
https://gist.github.com/thebrittfactor/0cfcacbd1366b927c268d0c41b86781b

Especially Medium-High detections in the Automated Finding Report should be taken into account;

### High Risk Issues
|Id|Title|Instances|
|:--:|:-------|:--:|
|[H-01]| Missing slippage checks | 4 |

### Medium Risk Issues
|Id|Title|Instances|
|:--:|:-------|:--:|
|[M-01]| Missing staleness checks for Chainlink oracle | 2 |
|[M-02]| Chainlink oracle will return the wrong price if the aggregator hits `minAnswer` | 2 |
|[M-03]| Return values of `transfer`/`transferFrom` not checked | 2 |
|[M-04]| Some functions do not work correctly with fee-on-transfer tokens | 2 |
|[M-05]| Some `ERC20` can revert on a zero value `transfer` | 1 |
|[M-06]| Non-compliant `IERC20` tokens may revert with `transfer` | 2 |
|[M-07]| Centralization issue caused by admin privileges | 26 |

### k) Gas Optimization
The project is generally efficient in terms of gas optimizations, many generally accepted gas optimizations have been implemented, gas optimizations with minor effects are already mentioned in automatic finding, but gas optimizations will not be a priority considering code readability and code base size

### l) New insights and learning from this audit 
- Invariant tests are of high quality within the scope of the project's testing, I have gained deep knowledge about this situation, which I have rarely seen in projects before.
- Amphora's core is based on a fork of the "Interest Protocol" by Gfx Labs.I learned detailed information about this topic.
- Using Chainlink and Uniswap v3 oracle in CDP borrow/lend protocols

**Time spent**<br>
12 hours

**[0xShaito (Amphora) confirmed](https://github.com/code-423n4/2023-07-amphora-findings/issues/381#issuecomment-1667529008)**



***

# Disclosures

C4 is an open organization governed by participants in the community.

C4 Audits incentivize the discovery of exploits, vulnerabilities, and bugs in smart contracts. Security researchers are rewarded at an increasing rate for finding higher-risk issues. Audit submissions are judged by a knowledgeable security researcher and solidity developer and disclosed to sponsoring developers. C4 does not conduct formal verification regarding the provided code but instead provides final verification.

C4 does not provide any guarantee or warranty regarding the security of this project. All smart contract software should be used at the sole risk and responsibility of users.
