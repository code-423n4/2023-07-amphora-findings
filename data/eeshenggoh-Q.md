## Summary
 Unsafe ERC20 Operation(s)

## Vulnerability Details

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

To circumvent ERC20's approve functions race-condition vulnerability use OpenZeppelin's SafeERC20 library's safe{Increase|Decrease}Allowance functions.


```
File: core\solidity\contracts\core\AMPHClaimer.sol
128:   function recoverDust(address _token, uint256 _amount) external override onlyOwner {
129:     IERC20(_token).transfer(owner(), _amount);
130: 
131:     emit RecoveredDust(_token, owner(), _amount);
132:   }
```
```
File: core\solidity\contracts\core\USDA.sol
101:   function _deposit(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
102:     if (_susdAmount == 0) revert USDA_ZeroAmount();
103:     sUSD.transferFrom(_msgSender(), address(this), _susdAmount);
104:     _mint(_target, _susdAmount);
105:     // Account for the susd received
106:     reserveAmount += _susdAmount;
107: 
108:     emit Deposit(_target, _susdAmount);
109:   }
```
```
File: core\solidity\contracts\core\USDA.sol
147:   function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
148:     if (reserveAmount == 0) revert USDA_EmptyReserve();
149:     if (_susdAmount == 0) revert USDA_ZeroAmount();
150:     if (_susdAmount > this.balanceOf(_msgSender())) revert USDA_InsufficientFunds();
151:     // Account for the susd withdrawn
152:     reserveAmount -= _susdAmount;
153:     sUSD.transfer(_target, _susdAmount);
154:     _burn(_msgSender(), _susdAmount);
155: 
156:     emit Withdraw(_target, _susdAmount);
157:   }
```
```
File: core\solidity\contracts\core\USDA.sol
202:   function donate(uint256 _susdAmount) external override paysInterest whenNotPaused {
203:     if (_susdAmount == 0) revert USDA_ZeroAmount();
204:     // Account for the susd received
205:     reserveAmount += _susdAmount;
206:     sUSD.transferFrom(_msgSender(), address(this), _susdAmount);
207:     _donation(_susdAmount);
208:   }
```
```
File: core\solidity\contracts\core\USDA.sol
212:   function recoverDust(address _to) external onlyOwner {
213:     // All sUSD sent directly to the contract is not accounted into the reserveAmount
214:     // This function allows governance to recover it
215:     uint256 _amount = sUSD.balanceOf(address(this)) - reserveAmount;
216:     sUSD.transfer(_to, _amount);
217: 
218:     emit RecoveredDust(owner(), _amount);
219:   }
```
```
File: core\solidity\contracts\core\USDA.sol
239:   function vaultControllerTransfer(
240:     address _target,
241:     uint256 _susdAmount
242:   ) external override onlyVaultController whenNotPaused {
243:     // Account for the susd withdrawn
244:     reserveAmount -= _susdAmount;
245:     // ensure transfer success
246:     sUSD.transfer(_target, _susdAmount);
247: 
248:     emit VaultControllerTransfer(_target, _susdAmount);
249:   }

```
```
File: core\solidity\contracts\core\Vault.sol
192:         if (_earnedReward != 0) {
193:           _virtualReward.getReward();
194:           _rewardToken.transfer(_msgSender(), _earnedReward);
195:           emit ClaimedReward(address(_rewardToken), _earnedReward);
196:         }
```
```
File: core\solidity\contracts\core\Vault.sol
212:           CRV.approve(address(_amphClaimer), _takenCRV);
```
```
File: core\solidity\contracts\core\Vault.sol
213:           CVX.approve(address(_amphClaimer), _takenCVX);
```
```
File: core\solidity\contracts\core\Vault.sol
223:       if (_totalCvxReward > 0) CVX.transfer(_msgSender(), _totalCvxReward);
```
```
File: core\solidity\contracts\core\Vault.sol
224:       if (_totalCrvReward > 0) CRV.transfer(_msgSender(), _totalCrvReward);
```
```
File: core\solidity\contracts\core\Vault.sol
320:   function _depositAndStakeOnConvex(address _token, IBooster _booster, uint256 _amount, uint256 _poolId) internal {
321:     IERC20(_token).approve(address(_booster), _amount);
322:     if (!_booster.deposit(_poolId, _amount, true)) revert Vault_DepositAndStakeOnConvexFailed();
323:   }
```
## Recommended Mitigation Steps##########################################################

```
File: core\solidity\contracts\core\AMPHClaimer.sol

import {SafeERC20} from "openzeppelin/token/utils/SafeERC20.sol";

128:   function recoverDust(address _token, uint256 _amount) external override onlyOwner {
129:    IERC20(_token).safeTransfer(owner(), _amount);
130: 
131:     emit RecoveredDust(_token, owner(), _amount);
132:   }

```
```
File: core\solidity\contracts\core\USDA.sol

import {SafeERC20} from "openzeppelin/token/utils/SafeERC20.sol";
101:   function _deposit(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
102:     if (_susdAmount == 0) revert USDA_ZeroAmount();
103:     sUSD.safeTransferFrom(_msgSender(), address(this), _susdAmount);
104:     _mint(_target, _susdAmount);
105:     // Account for the susd received
106:     reserveAmount += _susdAmount;
107: 
108:     emit Deposit(_target, _susdAmount);
109:   }
```
```
File: core\solidity\contracts\core\USDA.sol
147:   function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
148:     if (reserveAmount == 0) revert USDA_EmptyReserve();
149:     if (_susdAmount == 0) revert USDA_ZeroAmount();
150:     if (_susdAmount > this.balanceOf(_msgSender())) revert USDA_InsufficientFunds();
151:     // Account for the susd withdrawn
152:     reserveAmount -= _susdAmount;
153:     sUSD.safeTransfer(_target, _susdAmount);
154:     _burn(_msgSender(), _susdAmount);
155: 
156:     emit Withdraw(_target, _susdAmount);
157:   }

```
```
File: core\solidity\contracts\core\USDA.sol
202:   function donate(uint256 _susdAmount) external override paysInterest whenNotPaused {
203:     if (_susdAmount == 0) revert USDA_ZeroAmount();
204:     // Account for the susd received
205:     reserveAmount += _susdAmount;
206:     sUSD.safeTransferFrom(_msgSender(), address(this), _susdAmount);
207:     _donation(_susdAmount);
208:   }
```
```
File: core\solidity\contracts\core\USDA.sol
212:   function recoverDust(address _to) external onlyOwner {
213:     // All sUSD sent directly to the contract is not accounted into the reserveAmount
214:     // This function allows governance to recover it
215:     uint256 _amount = sUSD.balanceOf(address(this)) - reserveAmount;
216:     sUSD.safeTransfer(_to, _amount);
217: 
218:     emit RecoveredDust(owner(), _amount);
219:   }
```
```
File: core\solidity\contracts\core\USDA.sol
239:   function vaultControllerTransfer(
240:     address _target,
241:     uint256 _susdAmount
242:   ) external override onlyVaultController whenNotPaused {
243:     // Account for the susd withdrawn
244:     reserveAmount -= _susdAmount;
245:     // ensure transfer success
246:     sUSD.safeTransfer(_target, _susdAmount);
247: 
248:     emit VaultControllerTransfer(_target, _susdAmount);
249:   }

```
```
File: core\solidity\contracts\core\Vault.sol
192:         if (_earnedReward != 0) {
193:           _virtualReward.getReward();
194:           _rewardToken.safeTransfer(_msgSender(), _earnedReward);
195:           emit ClaimedReward(address(_rewardToken), _earnedReward);
196:         }
```
---
``
File: core\solidity\contracts\core\Vault.sol
212:           CRV.safeApprove(address(_amphClaimer), _takenCRV);
```
```
File: core\solidity\contracts\core\Vault.sol
213:           CVX.safeApprove(address(_amphClaimer), _takenCVX);
```
```
File: core\solidity\contracts\core\Vault.sol
223:       if (_totalCvxReward > 0) CVX.safeTransfer(_msgSender(), _totalCvxReward);
```
```
File: core\solidity\contracts\core\Vault.sol
224:       if (_totalCrvReward > 0) CRV.safeTransfer(_msgSender(), _totalCrvReward);
```
```
File: core\solidity\contracts\core\Vault.sol
320:   function _depositAndStakeOnConvex(address _token, IBooster _booster, uint256 _amount, uint256 _poolId) internal {
321:     IERC20(_token).safeApprove(address(_booster), _amount);
322:     if (!_booster.deposit(_poolId, _amount, true)) revert Vault_DepositAndStakeOnConvexFailed();
323:   }
```

## Summary
Unspecific Compiler Version Pragma

## Vulnerability Details

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

/core/solidity/contracts/core/AMPHClaimer.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/core/USDA.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/core/Vault.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/core/VaultController.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/core/VaultDeployer.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/core/WUSDA.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/governance/AmphoraProtocolToken.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/governance/GovernorCharlie.sol::3 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/CurveMaster.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/CTokenOracle.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/OracleRelay.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/TriCrypto2Oracle.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol::2 => pragma solidity >=0.5.0
/core/solidity/contracts/periphery/oracles/UniswapV3TokenOracleRelay.sol::2 => pragma solidity >=0.5.0
/core/solidity/contracts/periphery/oracles/WstEthOracle.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/utils/ExponentialNoError.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/utils/GovernanceStructs.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/utils/Roles.sol::2 => pragma solidity >=0.8.4 <0.9.0;
/core/solidity/contracts/utils/ThreeLines0_100.sol::2 => pragma solidity ^0.8.9;
/core/solidity/contracts/utils/UFragments.sol::3 => pragma solidity ^0.8.9;