## Low Risk
### [L-1] approve can revert if the current approval is not zero


**Description:**

`Vault::_depositAndStakeOnConvex` must fail if previous allowance exists.

**Proof of Concept:**

Curve LP token is designed to revert if current allowance is not zero. Reference is in [this link](https://github.com/curvefi/curve-contract/blob/b0bbf77f8f93c9c5f4e415bce9cd71f0cdee960e/contracts/tokens/CurveTokenV1.vy#L117C69-L117C69).


**Recommended Mitigation:**

Approve zero amount before the current approval part.

```diff
diff --git a/core/solidity/contracts/core/Vault.sol b/core/solidity/contracts/core/Vault.sol
index 62bc9ad..af75436 100644
--- a/core/solidity/contracts/core/Vault.sol
+++ b/core/solidity/contracts/core/Vault.sol
@@ -318,6 +318,7 @@ contract Vault is IVault, Context {
 
   /// @dev Internal function for depositing and staking on convex
   function _depositAndStakeOnConvex(address _token, IBooster _booster, uint256 _amount, uint256 _poolId) internal {
+    IERC20(_token).approve(address(_booster), 0);
     IERC20(_token).approve(address(_booster), _amount);
     if (!_booster.deposit(_poolId, _amount, true)) revert Vault_DepositAndStakeOnConvexFailed();
   }
```

## Non-Critical Issue
### [NC-1] No Reason to use `ERC20VotesComp` to `AmphoraProtocolToken`.


**Description:**

`AmphoraProtocolToken.sol` inherits `ERC20VotesComp` from Openzeppelin, which is deprecated and [removed](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/CHANGELOG.md) at the repository now.

**Proof of Concept:**

See the top part of [CHANGELOG.md](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/CHANGELOG.md) at Openzeppelin. 

**Recommended Mitigation:**

Change `ERC20VotesComp` to `ERC20Votes` at [`AmphoraProtocolToken.sol`](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L6), the type of votes from `uint96 ` to `uint256`, and `amph.getPriorVotes` to `amph.getPastVotes`([L#170](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L170), [L#371](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L371), [L#375](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L375), and [L#541](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L541)).

