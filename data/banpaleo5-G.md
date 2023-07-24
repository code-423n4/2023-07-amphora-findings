# Table of Contents
| Number | Issues Details                                                                                         | Count |
| :----- | :----------------------------------------------------------------------------------------------------- | :---- |
| [G-1] | Enhance gas efficiency by constructing the `DOMAIN_TYPEHASH` within the `initialize()` function | 1 |
| [G-2] | Leverage the rules of short-circuit evaluation for optimization benefits | 1 |
| [G-3] | Recommended to use `assembly` for writing address storage values | 43 |
| [G-4] | If EIP-2771 isn't being supported, avoid using the `_msgSender()` function | 17 |
| [G-5] | Employing `unchecked` blocks can lead to gas savings when operations cannot overflow/underflow | 1 |


## [G-1]</a><a name="G-1"> Enhance gas efficiency by constructing the `DOMAIN_TYPEHASH` within the `initialize()` function

<details>
<summary><i>There are 1 instances of this issue:</i></summary>

```solidity
File: GovernorCharlie.sol
bytes32 public constant DOMAIN_TYPEHASH =
```
</details>

## [G-2]</a><a name="G-2"> Leverage the rules of short-circuit evaluation for optimization benefits

<details>
<summary><i>There are 1 instances of this issue:</i></summary>

```solidity
File: GovernorCharlie.sol
(amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender))
(_emergency && !isWhitelisted(msg.sender))
```
</details>

## [G-3]</a><a name="G-3"> Recommended to use `assembly` for writing address storage values

<details>
<summary><i>There are 10 instances of this issue:</i></summary>

```solidity
File: CbEthEthOracle.sol
_vp = virtualPrice;
```

```solidity
File: VaultController.sol
_enabledTokens = enabledTokens;
```

```solidity
File: VaultController.sol
_i = _start; _i < _end;) {
```

```solidity
File: VaultController.sol
_oldClaimerContract = claimerContract;
```

```solidity
File: VaultController.sol
_oldBorrowingFee = initialBorrowingFee;
```

```solidity
File: VaultController.sol
_oldLiquidationFee = liquidationFee;
```

```solidity
File: VaultController.sol
_tokensToLiquidate = _tokenAmount;
```

```solidity
File: VaultController.sol
_toLiquidate = _tokensToLiquidate;
```

```solidity
File: VaultController.sol
_tokensToLiquidate = _maxTokensToLiquidate;
```

```solidity
File: VaultController.sol
_tokensToLiquidate = _balance;
```

```solidity
File: VaultController.sol
_actualTokensToLiquidate = _tokensToLiquidate;
```

```solidity
File: VaultController.sol
_stop = vaultsMinted;
```

```solidity
File: VaultController.sol
_i = _start; _i <= _stop;) {
```

```solidity
File: WUSDA.sol
_usdaAddress = USDA;
```

```solidity
File: EthSafeStableCurveOracle.sol
_value = virtualPrice;
```

```solidity
File: USDA.sol
__gonsPerFragment = _gonsPerFragment;
```

```solidity
File: USDA.sol
_totalSupply = MAX_SUPPLY;
```

```solidity
File: AMPHClaimer.sol
_cvxAmountToSend = _cvxRewardsFeeToExchange;
```

```solidity
File: AMPHClaimer.sol
_crvAmountToSend = _crvRewardsFeeToExchange;
```

```solidity
File: AMPHClaimer.sol
_claimableAmph = _amphByCrv;
```

```solidity
File: AMPHClaimer.sol
_tempAmountReceived = _tokenAmountToSend; // CRV, 1e18
```

```solidity
File: AMPHClaimer.sol
_distributedAmph = distributedAmph;
```

```solidity
File: AMPHClaimer.sol
_amphForThisTurn = _amphAvailableForThisCliff;
```

```solidity
File: AMPHClaimer.sol
_amphAmount = _amphToMint;
```

```solidity
File: ChainlinkOracleRelay.sol
_stale = true;
```

```solidity
File: CurveMaster.sol
_oldCurveAddress = vaultControllerAddress;
```

```solidity
File: GovernorCharlie.sol
_callData = _data;
```

```solidity
File: GovernorCharlie.sol
_numberOfVotes = _votes;
```

```solidity
File: GovernorCharlie.sol
_oldTimelockDelay = proposalTimelockDelay;
```

```solidity
File: GovernorCharlie.sol
_oldEmergencyTimelockDelay = emergencyTimelockDelay;
```

```solidity
File: GovernorCharlie.sol
_oldVotingDelay = votingDelay;
```

```solidity
File: GovernorCharlie.sol
_oldVotingPeriod = votingPeriod;
```

```solidity
File: GovernorCharlie.sol
_oldEmergencyVotingPeriod = emergencyVotingPeriod;
```

```solidity
File: GovernorCharlie.sol
_oldProposalThreshold = proposalThreshold;
```

```solidity
File: GovernorCharlie.sol
_oldQuorumVotes = quorumVotes;
```

```solidity
File: GovernorCharlie.sol
_oldEmergencyQuorumVotes = emergencyQuorumVotes;
```

```solidity
File: GovernorCharlie.sol
_oldGuardian = whitelistGuardian;
```

```solidity
File: GovernorCharlie.sol
_oldOptimisticVotingDelay = optimisticVotingDelay;
```

```solidity
File: GovernorCharlie.sol
_oldOptimisticQuorumVotes = optimisticQuorumVotes;
```

```solidity
File: GovernorCharlie.sol
_delay = proposalTimelockDelay;
```

```solidity
File: Vault.sol
_canStake = true;
```

```solidity
File: Vault.sol
_newLiability = baseLiability;
```

```solidity
File: Vault.sol
_cvxAmount = _amtTillMax;
```
</details>

## [G-4]</a><a name="G-4"> If EIP-2771 isn't being supported, avoid using the `_msgSender()` function

<details>
<summary><i>There are 17 instances of this issue:</i></summary>

```solidity
File: USDA.sol
emit Donation(_msgSender(), _amount, _totalSupply);
```

```solidity
File: VaultController.sol
_borrow(_id, _amount, _msgSender(), true);
```

```solidity
File: USDA.sol
sUSD.transferFrom(_msgSender(), address(this), _susdAmount);
```

```solidity
File: VaultController.sol
if (_msgSender() != vaultIdVaultAddress[_vaultID]) revert VaultController_NotValidVault();
```

```solidity
File: USDA.sol
_burn(_msgSender(), _susdAmount);
```

```solidity
File: VaultController.sol
usda.vaultControllerBurn(_msgSender(), _usdaToRepurchase);
_vault.controllerTransfer(_assetAddress, _msgSender(), _tokensToLiquidate - _liquidationFee);
```

```solidity
File: WUSDA.sol
uint256 _wusdaAmount = balanceOf(_msgSender());
_withdraw(_msgSender(), _to, _usdaAmount, _wusdaAmount);
```

```solidity
File: WUSDA.sol
_withdraw(_msgSender(), _to, _usdaAmount, _wusdaAmount);
```

```solidity
File: USDA.sol
_withdraw(_susdAmount, _msgSender());
```

```solidity
File: WUSDA.sol
_deposit(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
```

```solidity
File: USDA.sol
sUSD.transferFrom(_msgSender(), address(this), _susdAmount);
```

```solidity
File: VaultController.sol
if (_msgSender() != _vault.minter()) revert VaultController_OnlyMinter();
```

```solidity
File: Vault.sol
SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_tokenAddress), _msgSender(), _amount);
```

```solidity
File: WUSDA.sol
_deposit(_msgSender(), _to, _usdaAmount, _wusdaAmount);
```

```solidity
File: USDA.sol
_mint(_msgSender(), _susdAmount);
```

```solidity
File: WUSDA.sol
_withdraw(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
```

```solidity
File: WUSDA.sol
_wusdaAmount = balanceOf(_msgSender());
_withdraw(_msgSender(), _to, _usdaAmount, _wusdaAmount);
```
</details>

## [G-5]</a><a name="G-5"> Employing `unchecked` blocks can lead to gas savings when operations cannot overflow/underflow

<details>
<summary><i>There are 1 instances of this issue:</i></summary>

```solidity
File: AnchoredViewRelay.sol
uint256 _lowerBounds = _anchorPrice - _buffer;
```
</details>



