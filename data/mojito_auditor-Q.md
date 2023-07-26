# Summary

| Id | Title |
| --- | --- |
| L-1 | VaultDeployer constructor is payable but there is no way to get ETH out |
| L-2 | Code and comment do not match in function GovernorCharlie.cancel() |
| L-3 | Incorrect Collateral Type When Owner Uses `updateRegisteredErc20()` |

# L-1. VaultDeployer constructor is payable but there is no way to get ETH out

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultDeployer.sol#L18

## Detail

In the `VaultDeployer` contract, the constructor is `payable` and can receive ETH. However, ETH is not used anywhere in the contract and there is no function to withdraw it either.

```solidity
/// @param _cvx The address of the CVX token
/// @param _crv The address of the CRV token
constructor(IERC20 _cvx, IERC20 _crv) payable { // @audit payable but no way to get ETH out
  CVX = _cvx;
  CRV = _crv;
}

```

## Recommendation

Consider removing `payable` or adding a function to withdraw ETH.

# L-2. Code and comment do not match in function `GovernorCharlie.cancel()`

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L361-L379

## Detail

In the function `cancel()`, the comment says:

> Whitelisted proposers can't be canceled for falling below proposal threshold.
> 

However, the code still allows the whitelisted proposals to be cancelled:

```solidity
// Whitelisted proposers can't be canceled for falling below proposal threshold
if (isWhitelisted(_proposal.proposer)) {
    // @audit Contradict with the comment above.
    //        If it falls below proposal threshold, it won't revert and will be allowed to be cancelled
    if (
      (amph.getPriorVotes(_proposal.proposer, (block.number - 1)) >= proposalThreshold)
        || msg.sender != whitelistGuardian
    ) revert GovernorCharlie_WhitelistedProposer();
}

```

## Recommendation

Consider fixing the code or updating the comment to match it.

# L-3. Incorrect Collateral Type When Owner Uses `updateRegisteredErc20()`

https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L395

## Details

In the VaultController, when the owner registers a new ERC20 as collateral, its type is chosen automatically based on the `poolId`. If the `poolId` is `0`, its collateral type is `Single`, and in other cases, it is `CurveLPStakedOnConvex`.

However, in the function `updateRegisteredErc20()`, the owner can change the `poolId`, but the collateral type is not updated accordingly.

```solidity
// @audit Collateral type could be wrong if `poolId` changes from non-zero to zero or vice-versa
if (_poolId != 0) {
  (address _lpToken,,, address _crvRewards,,) = BOOSTER.poolInfo(_poolId);
  if (_lpToken != _tokenAddress) revert VaultController_TokenAddressDoesNotMatchLpAddress();
  _collateral.collateralType = CollateralType.CurveLPStakedOnConvex;
  _collateral.crvRewardsContract = IBaseRewardPool(_crvRewards);
  _collateral.poolId = _poolId;
}

```

## Recommendation

Consider adding a check to ensure that `_poolId` cannot change from a non-zero value to zero or vice versa.
