#### **[L-01]** Withdrawing ERC20 from the Vault does not claim the rewards 
Simply, because of this false `withdrawAndUnwrap(_amount, false)`  [withdrawERC20](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L117-L133) is only withdrawing the funds from convex and leaving the rewards. For users to claim them, they need to call another function [claimRewards](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229), this is unneeded, since `withdrawERC20` can be slightly modified to claim rewards on withdraw if the user wants, executing withdraw + claim in one TX instead of two. 
You can modifiy it as follows:
```jsx
  function withdrawERC20(address _tokenAddress, uint256 _amount, bool claimRewards) external override onlyMinter {
    if (CONTROLLER.tokenId(_tokenAddress) == 0) revert Vault_TokenNotRegistered();
    if (isTokenStaked[_tokenAddress]) {
+     if(claimRewards){
+       address[] rewardT = new address[](1)
+       rewardT[0] = _tokenAddress;
+       claimRewards(rewardT);
+     }else{
        if (!CONTROLLER.tokenCrvRewardsContract(_tokenAddress).withdrawAndUnwrap(_amount, false)) {
          revert Vault_WithdrawAndUnstakeOnConvexFailed();
        }
+     }
    }
```

---

#### **[L-02]** User inconvenience when calling [Vault.canStake](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L156-L159)
From the `canStake` function we can see that before returning true it checks  if the pool exists, within the controller does the vault have any balance of such tokens and where the issue is, if the token is **already staked**  -  `!isTokenStaked[_token]` - where the function reverts if there is a stake on the token. This means if a User stakes a small amount of a given token and forgets about it, and after some time when he wants to stake more of the same token he will most likely call `canStake` to check if he would be able to stake, but `canStake` will return false dues to this  `!isTokenStaked[_token]`.
```jsx
  function canStake(address _token) external view override returns (bool _canStake) {
    uint256 _poolId = CONTROLLER.tokenPoolId(_token);
    if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;
  }
```
You can remove `!isTokenStaked[_token]` from the function, since there is no need for it.

---

#### **[L-03]** If one of the tokens in `StableCurveLpOracle` is undervalued it will report less price for the whole group
Under [_get](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L41-L49) there is a for loop and a check inside of it which uses `Math.min` to determine the lowest price for one of the tokens. This price is later send as a return by  `StableCurveLpOracle`   oracle.
```jsx
  function _get() internal view returns (uint256 _value) {
    uint256 _minStable = anchoredUnderlyingTokens[0].peekValue();
    for (uint256 _i = 1; _i < anchoredUnderlyingTokens.length;) {
      _minStable = Math.min(_minStable, anchoredUnderlyingTokens[_i].peekValue());
      unchecked {
        ++_i;
      }
    }
```
As this is not the best implementation since if one of these tokens is undervalued it will return lower price for all of them. Better implementation is to average them out.
Example:

|        | DAI  | USDC | USDT  |
|--------|------|------|-------|
| Prices | 1.00 | 1.00 | 0.995 |

This will return **0.995** since **USDT** is the lowest value and Math.min will catch it
But if the 3 values were averaged (**( a + b + c ) / 3** ) it would return **0.9983**

And finally current price (in the time of witting this) of [3pool](https://coinmarketcap.com/currencies/lp-3pool-curve/) is **1.026** which is gonna make a huge difference in the calculations.

The difference, which is not much,but the scenario above happens constantly since 0.995 is easily reachable with these stables, it's gonna be even worse if let's say  **USDC** and **DAI** are above 1$ and **USDT** is bellow, but you'v got the picture.

---

#### **[L-04]** A whitelisted proposer can make emergency proposals.
If a whitelisted proposer tries to make an **emergency** proposal, the **_emergency** field of the proposal's struct will stay true even though whitelisted proposers cannot make emergency proposals. Consider setting the **_emergency** field to **false** in the check that handles whitelisted users. 
```jsx
if (_emergency && !isWhitelisted(msg.sender)) {
      _newProposal.startBlock = block.number;
      _newProposal.endBlock = block.number + emergencyVotingPeriod;
      _newProposal.quorumVotes = emergencyQuorumVotes;
      _newProposal.delay = emergencyTimelockDelay;
}
```

[Open code](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L208C1-L208C1)

---

#### **[L-05]** The owner of a **VaultController** can go from a Single collateral to a **CurveLPStakedOnConvex**, but not the other way around. 
It's problematic because if an admin tries to set the collateral type from **CurveLPStakedOnConvex** to Single, the function will not revert, but will instead update half the fields with the provided information and leave the pool information exactly how it has been. Consider reverting if this is an intended feature, otherwise let owner update the collateral type
```jsx
if (_poolId != 0) {
      (address _lpToken,,, address _crvRewards,,) = BOOSTER.poolInfo(_poolId);
      if (_lpToken != _tokenAddress) revert VaultController_TokenAddressDoesNotMatchLpAddress();
      _collateral.collateralType = CollateralType.CurveLPStakedOnConvex;
      _collateral.crvRewardsContract = IBaseRewardPool(_crvRewards);
      _collateral.poolId = _poolId;
}
```

[Open code](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L398C6-L398C6)