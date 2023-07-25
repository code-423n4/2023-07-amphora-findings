
## Gas Optimizations
### [G-1] Expand the range of unchecked in `VaultController::getCollateralInfo`

Apply the following git diff:

```diff
diff --git a/core/solidity/contracts/core/VaultController.sol b/core/solidity/contracts/core/VaultController.sol
index 95e3009..be6fa2b 100644
--- a/core/solidity/contracts/core/VaultController.sol
+++ b/core/solidity/contracts/core/VaultController.sol
@@ -235,12 +235,13 @@ contract VaultController is Pausable, IVaultController, ExponentialNoError, Owna
     uint256 _enabledTokensLength = enabledTokens.length;
     _end = _enabledTokensLength < _end ? _enabledTokensLength : _end;
 
-    _collateralsInfo = new CollateralInfo[](_end - _start);
-
+    unchecked {
+      _collateralsInfo = new CollateralInfo[](_end - _start);
+    }
+    
     for (uint256 _i = _start; _i < _end;) {
-      _collateralsInfo[_i - _start] = tokenAddressCollateralInfo[enabledTokens[_i]];
-
       unchecked {
+        _collateralsInfo[_i - _start] = tokenAddressCollateralInfo[enabledTokens[_i]];
         ++_i;
       }
     }

```

### [G-2] Add unchecked to `Vault::modifyLiability`

Apply the following git diff:

```diff
diff --git a/core/solidity/contracts/core/Vault.sol b/core/solidity/contracts/core/Vault.sol
index 62bc9ad..5e76e89 100644
--- a/core/solidity/contracts/core/Vault.sol
+++ b/core/solidity/contracts/core/Vault.sol
@@ -311,7 +311,9 @@ contract Vault is IVault, Context {
     } else {
       // require statement only valid for repayment
       if (baseLiability < _baseAmount) revert Vault_RepayTooMuch();
-      baseLiability -= _baseAmount;
+      unchecked {
+        baseLiability -= _baseAmount;
+      }
     }
     _newLiability = baseLiability;
   }

```

