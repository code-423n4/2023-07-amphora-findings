

### Low Issues
|Title|Issue|Instances|
|-|:-|:-:|
|[L-1] Burn functions must be protected with a modifier | [Burn functions must be protected with a modifier](#burn-functions-must-be-protected-with-a-modifier) | 4 |
|[L-2] Divide before multiply | [Divide before multiply](#divide-before-multiply) | 2 |
|[L-3] ERC20 Approve Call is Not Safe | [ERC20 Approve Call is Not Safe](#erc20-approve-call-is-not-safe) | 3 |
|[L-4] Fee/Rate Caps Missing in Smart Contracts | [Fee/Rate Caps Missing in Smart Contracts](#feerate-caps-missing-in-smart-contracts) | 2 |
|[L-5] Calls inside a loop | [Calls inside a loop](#calls-inside-a-loop) | 21 |
|[L-6] Reentrancy vulnerabilities | [Reentrancy vulnerabilities](#reentrancy-vulnerabilities-2) | 14 |
|[L-7] Arrays can grow without a way to shrink them | [Arrays can grow without a way to shrink them](#arrays-can-grow-without-a-way-to-shrink-them) | 1 |
|[L-8] Unsafe Cast Unsigned to Signed | [Unsafe Cast Unsigned to Signed](#unsafe-cast-unsigned-to-signed) | 3 |
|[L-9] Zero-value transfers may revert | [Zero-value transfers may revert](#zero-value-transfers-may-revert) | 5 |

Total: 9 issues


### Non-Critical Issues
|Title|Issue|Instances|
|-|:-|:-:|
|[N-1] Do not calculate constants | [Do not calculate constants](#do-not-calculate-constants) | 7 |
|[N-2] Cyclomatic complexity | [Cyclomatic complexity](#cyclomatic-complexity) | 1 |
|[N-3] Dead-code | [Dead-code](#dead-code) | 1 |
|[N-4] Else block not required | [Else block not required](#else-block-not-required) | 7 |
|[N-5] Hardcoded addresses in contract | [Hardcoded addresses in contract](#hardcoded-addresses-in-contract) | 3 |
|[N-6] Interfaces Should Be Defined in Separate Files From Their Usage | [Interfaces Should Be Defined in Separate Files From Their Usage](#interfaces-should-be-defined-in-separate-files-from-their-usage) | 1 |
|[N-7] Token contract should have a blacklist function or modifier | [Token contract should have a blacklist function or modifier](#token-contract-should-have-a-blacklist-function-or-modifier) | 4 |
|[N-8] Missing inheritance | [Missing inheritance](#missing-inheritance) | 3 |
|[N-9] Redundant Inheritance | [Redundant Inheritance](#redundant-inheritance) | 1 |

Total: 9 issues

## Burn functions must be protected with a modifier
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
Burn function without a protective modifier.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L188-L198](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L188-L198)


- File: solidity/contracts/core/WUSDA.sol
```
 
Line: 79          function burn(uint256 _wusdaAmount) external override returns (uint256 _usdaAmount) 
```
Burn function without a protective modifier.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L79-L82](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L79-L82)


- File: solidity/interfaces/core/IUSDA.sol
```
 
Line: 194          function burn(uint256 _susdAmount) external;
```
Burn function without a protective modifier.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol#L194](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol#L194)


- File: solidity/interfaces/core/IWUSDA.sol
```
 
Line: 78          function burn(uint256 _wusdaAmount) external returns (uint256 _usdaAmount);
```
Burn function without a protective modifier.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IWUSDA.sol#L78](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IWUSDA.sol#L78)


</details>

# 

## Divide before multiply
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
 performs a multiplication on the result of a division:
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
 performs a multiplication on the result of a division:
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

# 

## ERC20 Approve Call is Not Safe
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

# 


## Fee/Rate Caps Missing in Smart Contracts
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

# 

## Calls inside a loop
- Severity: Low
- Confidence: Medium

### Description
Calls inside a loop might lead to a denial-of-service attack.

<details>

<summary>
There are 21 instances of this issue:

</summary>

###
- File: solidity/contracts/core/Vault.sol
```
 
Line: 164          function claimRewards(address[] memory _tokenAddresses) external override onlyMinter 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 170          CONTROLLER.tokenCollateralInfo(_tokenAddresses[_i])
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 164          function claimRewards(address[] memory _tokenAddresses) external override onlyMinter 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 177          _rewardsContract.earned(address(this))
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 164          function claimRewards(address[] memory _tokenAddresses) external override onlyMinter 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 182          _rewardsContract.getReward(address(this), false)
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 329          function _calculateCVXReward(uint256 _crv) internal view returns (uint256 _cvxAmount) 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 330          CVX.totalSupply()
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L329-L349](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L329-L349)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 329          function _calculateCVXReward(uint256 _crv) internal view returns (uint256 _cvxAmount) 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 331          CVX.totalCliffs()
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L329-L349](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L329-L349)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 329          function _calculateCVXReward(uint256 _crv) internal view returns (uint256 _cvxAmount) 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 337          _supply / CVX.reductionPerCliff()
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L329-L349](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L329-L349)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 329          function _calculateCVXReward(uint256 _crv) internal view returns (uint256 _cvxAmount) 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 346          CVX.maxSupply() - _supply
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L329-L349](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L329-L349)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 164          function claimRewards(address[] memory _tokenAddresses) external override onlyMinter 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 187          _rewardsContract.extraRewardsLength()
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 164          function claimRewards(address[] memory _tokenAddresses) external override onlyMinter 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 189          _rewardsContract.extraRewards(_j)
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 164          function claimRewards(address[] memory _tokenAddresses) external override onlyMinter 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 190          _virtualReward.rewardToken()
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 164          function claimRewards(address[] memory _tokenAddresses) external override onlyMinter 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 191          _virtualReward.earned(address(this))
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 164          function claimRewards(address[] memory _tokenAddresses) external override onlyMinter 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 193          _virtualReward.getReward()
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 164          function claimRewards(address[] memory _tokenAddresses) external override onlyMinter 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 194          _rewardToken.transfer(_msgSender(), _earnedReward)
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164-L229)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 234          function claimableRewards(address _tokenAddress) external view override returns (Reward[] memory _rewards) 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 255          _rewardsContract.extraRewards(_i)
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L234-L277](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L234-L277)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 234          function claimableRewards(address _tokenAddress) external view override returns (Reward[] memory _rewards) 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 256          _virtualReward.rewardToken()
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L234-L277](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L234-L277)


- File: solidity/contracts/core/Vault.sol
```
 
Line: 234          function claimableRewards(address _tokenAddress) external view override returns (Reward[] memory _rewards) 
```
 has external calls inside a loop: File: solidity/contracts/core/Vault.sol
```
 
Line: 257          _virtualReward.earned(address(this))
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L234-L277](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L234-L277)


- File: solidity/contracts/core/VaultController.sol
```
 
Line: 988          function vaultSummaries(
    uint96 _start,
    uint96 _stop
  ) public view override returns (VaultSummary[] memory _vaultSummaries) 
```
 has external calls inside a loop: File: solidity/contracts/core/VaultController.sol
```
 
Line: 999          _tokenBalances[_j] = _vault.balances(enabledTokens[_j])
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L988-L1012](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L988-L1012)


- File: solidity/contracts/core/VaultController.sol
```
 
Line: 894          function _peekVaultBorrowingPower(IVault _vault) private view returns (uint192 _borrowPower) 
```
 has external calls inside a loop: File: solidity/contracts/core/VaultController.sol
```
 
Line: 904          _vault.balances(_tokenAddress)
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L894-L916](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L894-L916)


- File: solidity/contracts/core/VaultController.sol
```
 
Line: 894          function _peekVaultBorrowingPower(IVault _vault) private view returns (uint192 _borrowPower) 
```
 has external calls inside a loop: File: solidity/contracts/core/VaultController.sol
```
 
Line: 907          _safeu192(_collateral.oracle.peekValue())
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L894-L916](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L894-L916)


- File: solidity/contracts/core/VaultController.sol
```
 
Line: 988          function vaultSummaries(
    uint96 _start,
    uint96 _stop
  ) public view override returns (VaultSummary[] memory _vaultSummaries) 
```
 has external calls inside a loop: File: solidity/contracts/core/VaultController.sol
```
 
Line: 1005          _vaultSummaries[_i - _start] =
        VaultSummary(_i, _peekVaultBorrowingPower(_vault), this.vaultLiability(_i), enabledTokens, _tokenBalances)
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L988-L1012](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L988-L1012)


- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 309          function execute(uint256 _proposalId) external payable override 
```
 has external calls inside a loop: File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 314          this.executeTransaction{value: _proposal.values[_i]}(
        _proposal.targets[_i], _proposal.values[_i], _proposal.signatures[_i], _proposal.calldatas[_i], _proposal.eta
      )
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L309-L320](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L309-L320)


</details>

# 

## Reentrancy vulnerabilities
- Severity: Low
- Confidence: Medium

### Description

Detection of the [reentrancy bug](https://github.com/trailofbits/not-so-smart-contracts/tree/master/reentrancy).
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
:
	External calls:
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

	State variables written after the call(s):
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
:
	External calls:
	- File: solidity/contracts/core/USDA.sol
```
 
Line: 147          paysInterest
```

		- File: solidity/contracts/core/USDA.sol
```
 
Line: 49          IVaultController(_vaultControllers.at(_i)).calculateInterest()
```

	State variables written after the call(s):
	- File: solidity/contracts/core/USDA.sol
```
 
Line: 152          reserveAmount -= _susdAmount
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L147-L157](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L147-L157)


- Reentrancy in File: solidity/contracts/core/USDA.sol
```
 
Line: 147          function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused 
```
:
	External calls:
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

	State variables written after the call(s):
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
:
	External calls:
	- File: solidity/contracts/core/USDA.sol
```
 
Line: 182          paysInterest
```

		- File: solidity/contracts/core/USDA.sol
```
 
Line: 49          IVaultController(_vaultControllers.at(_i)).calculateInterest()
```

	State variables written after the call(s):
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
:
	External calls:
	- File: solidity/contracts/core/USDA.sol
```
 
Line: 202          paysInterest
```

		- File: solidity/contracts/core/USDA.sol
```
 
Line: 49          IVaultController(_vaultControllers.at(_i)).calculateInterest()
```

	State variables written after the call(s):
	- File: solidity/contracts/core/USDA.sol
```
 
Line: 205          reserveAmount += _susdAmount
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L202-L208](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L202-L208)


- Reentrancy in File: solidity/contracts/core/USDA.sol
```
 
Line: 202          function donate(uint256 _susdAmount) external override paysInterest whenNotPaused 
```
:
	External calls:
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

	State variables written after the call(s):
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
:
	External calls:
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
:
	External calls:
	- File: solidity/contracts/core/Vault.sol
```
 
Line: 285          SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_token), _to, _amount)
```

	State variables written after the call(s):
	- File: solidity/contracts/core/Vault.sol
```
 
Line: 286          balances[_token] -= _amount
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L284-L287](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L284-L287)


- Reentrancy in File: solidity/contracts/core/Vault.sol
```
 
Line: 89          function depositERC20(address _token, uint256 _amount) external override onlyMinter 
```
:
	External calls:
	- File: solidity/contracts/core/Vault.sol
```
 
Line: 92          SafeERC20Upgradeable.safeTransferFrom(IERC20Upgradeable(_token), _msgSender(), address(this), _amount)
```

	State variables written after the call(s):
	- File: solidity/contracts/core/Vault.sol
```
 
Line: 102          isTokenStaked[_token] = true
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L89-L109](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L89-L109)


- Reentrancy in File: solidity/contracts/core/Vault.sol
```
 
Line: 117          function withdrawERC20(address _tokenAddress, uint256 _amount) external override onlyMinter 
```
:
	External calls:
	- File: solidity/contracts/core/Vault.sol
```
 
Line: 120          !CONTROLLER.tokenCrvRewardsContract(_tokenAddress).withdrawAndUnwrap(_amount, false)
```

	State variables written after the call(s):
	- File: solidity/contracts/core/Vault.sol
```
 
Line: 125          balances[_tokenAddress] -= _amount
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L117-L133](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L117-L133)


- Reentrancy in File: solidity/contracts/core/VaultController.sol
```
 
Line: 278          function mintVault() public override whenNotPaused returns (address _vaultAddress) 
```
:
	External calls:
	- File: solidity/contracts/core/VaultController.sol
```
 
Line: 282          _vaultAddress = _createVault(vaultsMinted, _msgSender())
```

		- File: solidity/contracts/core/VaultController.sol
```
 
Line: 980          _vault = address(VAULT_DEPLOYER.deployVault(_id, _minter))
```

	State variables written after the call(s):
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
:
	External calls:
	- File: solidity/contracts/periphery/CurveMaster.sol
```
 
Line: 42          IVaultController(vaultControllerAddress).calculateInterest()
```

	State variables written after the call(s):
	- File: solidity/contracts/periphery/CurveMaster.sol
```
 
Line: 44          curves[_tokenAddress] = _curveAddress
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L41-L47](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L41-L47)


- Reentrancy in File: solidity/contracts/periphery/oracles/CbEthEthOracle.sol
```
 
Line: 70          function _updateVirtualPrice() internal 
```
:
	External calls:
	- File: solidity/contracts/periphery/oracles/CbEthEthOracle.sol
```
 
Line: 74          CB_ETH_POOL.claim_admin_fees()
```

	State variables written after the call(s):
	- File: solidity/contracts/periphery/oracles/CbEthEthOracle.sol
```
 
Line: 76          virtualPrice = _virtualPrice
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol#L70-L77](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol#L70-L77)


- Reentrancy in File: solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol
```
 
Line: 36          function _updateVirtualPrice() internal 
```
:
	External calls:
	- File: solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol
```
 
Line: 41          CRV_POOL.remove_liquidity(0, _amounts)
```

	State variables written after the call(s):
	- File: solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol
```
 
Line: 43          virtualPrice = _virtualPrice
```


[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol#L36-L44](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol#L36-L44)


</details>

# 


## Zero-value transfers may revert
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

# 



## Unsafe Cast Unsigned to Signed
- Severity: Low
- Confidence: High

### Description
An unsafe cast occurs when a variable or expression of an unsigned/signed integer is converted to a signed/unsigned integer without proper checks. This type of cast can lead to unexpected behavior and vulnerabilities in the code.

When an unsigned integer is cast to a signed integer, the underlying bit representation remains the same, but the interpretation of the value changes. This can result in overflow or wraparound issues, leading to incorrect calculations or security vulnerabilities.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
- File: solidity/contracts/core/VaultController.sol
```
 
Line: 937          int256(_ui18)
```
 usafe cast from uint256 to int256 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L937](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L937)


- File: solidity/contracts/core/VaultController.sol
```
 
Line: 942          uint256(_intCurveVal)
```
 usafe cast from int256 to uint256 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L942](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L942)


- File: solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol
```
 
Line: 14          uint256(_answer)
```
 usafe cast from int256 to uint256 

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol#L14](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol#L14)


</details>

# 

## Arrays can grow without a way to shrink them
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

# 



## Do not calculate constants
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

# 

## Cyclomatic complexity
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

# 


## Dead-code
- Severity: Non-Critical
- Confidence: Medium

### Description
Functions that are not sued.

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

# 



## Else block not required
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
No nees to declare else block.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L182-L190](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L182-L190)


- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 475          if (_proposal.executed) return ProposalState.Executed;
    else if (block.timestamp >= (_proposal.eta + GRACE_PERIOD)) return ProposalState.Expired
```
No nees to declare else block.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L475-L476](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L475-L476)


- File: solidity/contracts/governance/GovernorCharlie.sol
```
 
Line: 474          if (_proposal.eta == 0) return ProposalState.Succeeded;
    else if (_proposal.executed) return ProposalState.Executed;
    else if (block.timestamp >= (_proposal.eta + GRACE_PERIOD)) return ProposalState.Expired
```
No nees to declare else block.
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
No nees to declare else block.
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
No nees to declare else block.
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
No nees to declare else block.
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
No nees to declare else block.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L466-L476](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L466-L476)


</details>

# 


## Hardcoded addresses in contract
- Severity: Non-Critical
- Confidence: High

### Description
Addresses should not be hardcoded in contracts. Instead, they should be declared as immutable and assigned via constructor arguments. This practice keeps the code consistent across deployments on different networks and prevents the need for recompilation when addresses change.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###


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

# 



## Interfaces Should Be Defined in Separate Files From Their Usage
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

# 


## Token contract should have a blacklist function or modifier
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
Token contract does not contain a blacklist. Consider adding one for increased security.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L18-L302](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L18-L302)


- File: solidity/contracts/core/WUSDA.sol
```
 
Line: 28          contract WUSDA is IWUSDA, ERC20, ERC20Permit 
```
Token contract does not contain a blacklist. Consider adding one for increased security.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L28-L257](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L28-L257)


- File: solidity/contracts/governance/AmphoraProtocolToken.sol
```
 
Line: 8          contract AmphoraProtocolToken is IAmphoraProtocolToken, ERC20VotesComp, Ownable 
```
Token contract does not contain a blacklist. Consider adding one for increased security.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L8-L23](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L8-L23)


- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 20          contract UFragments is Ownable, IERC20Metadata 
```
Token contract does not contain a blacklist. Consider adding one for increased security.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L20-L341](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L20-L341)


</details>

# 


## Redundant Inheritance
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

# 


## Missing inheritance
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

# 

