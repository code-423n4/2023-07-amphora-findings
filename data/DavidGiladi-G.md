

### Gas Optimization Issues
|Title|Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
|[G-1] Inefficient use of abi.encode() | [Inefficient use of abi.encode()](#inefficient-use-of-abiencode) | 7 | 700 |
|[G-2] Avoid unnecessary storage updates | [Avoid unnecessary storage updates](#avoid-unnecessary-storage-updates) | 21 | 16800 |
|[G-3] Multiplication and Division by 2 Should use in Bit Shifting | [Multiplication and Division by 2 Should use in Bit Shifting](#multiplication-and-division-by-2-should-use-in-bit-shifting) | 2 | 40 |
|[G-4] Check Arguments Early | [Check Arguments Early](#check-arguments-early) | 10 | - |
|[G-5] Division operations between unsigned could be unchecked | [Division operations between unsigned could be unchecked](#division-operations-between-unsigned-could-be-unchecked) | 5 | 425 |
|[G-6] Cache External Calls in Loops | [Cache External Calls in Loops](#cache-external-calls-in-loops) | 2 | 200 |
|[G-7] Greater or Equal Comparison Costs Less Gas than Greater Comparison | [Greater or Equal Comparison Costs Less Gas than Greater Comparison](#greater-or-equal-comparison-costs-less-gas-than-greater-comparison) | 3 | 9 |
|[G-8] Using Storage Instead of Memory for structs/arrays Saves Gas | [Using Storage Instead of Memory for structs/arrays Saves Gas](#using-storage-instead-of-memory-for-structsarrays-saves-gas) | 5 | 21000 |
|[G-9] Optimal State Variable Order | [Optimal State Variable Order](#optimal-state-variable-order) | 1 | 5000 |
|[G-10] Short-circuit rules can be used to optimize some gas usage | [Short-circuit rules can be used to optimize some gas usage](#short-circuit-rules-can-be-used-to-optimize-some-gas-usage) | 1 | 2100 |
|[G-11] Unnecessary Casting of Variables | [Unnecessary Casting of Variables](#unnecessary-casting-of-variables) | 5 | - |

Total: 10 issues

#

## Inefficient use of abi.encode()
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

# 


## Multiplication and Division by 2 Should use in Bit Shifting
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 40

### Description
The expressions 'x * 2' and 'x / 2' can be optimized for gas efficiency by utilizing bitwise operations. In Solidity, you can achieve the same results by using bitwise left shift (x << 1) for multiplication and bitwise right shift (x >> 1) for division.

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

# 


## Avoid unnecessary storage updates
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

# 


## Optimal State Variable Order
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

# 


## Check Arguments Early
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

# 


## Cache External Calls in Loops
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

# 



## Greater or Equal Comparison Costs Less Gas than Greater Comparison
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
 using GREATER_EQUAL/LESS_EQUAL in this case is identical therefore should be use.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L132](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L132)


- File: solidity/contracts/core/USDA.sol
```
 
Line: 142          _susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance
```
 using GREATER_EQUAL/LESS_EQUAL in this case is identical therefore should be use.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L142](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L142)


- File: solidity/contracts/core/VaultController.sol
```
 
Line: 236          _end = _enabledTokensLength < _end ? _enabledTokensLength : _end
```
 using GREATER_EQUAL/LESS_EQUAL in this case is identical therefore should be use.

[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L236](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L236)


</details>

# 


## Using Storage Instead of Memory for structs/arrays Saves Gas
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

# 


## Short-circuit rules can be used to optimize some gas usage
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

# 



## Unnecessary Casting of Variables
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

# 


## Division operations between unsigned could be unchecked
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
 Should be unchecked - _distributedAmph / BASE_SUPPLY_PER_CLIFF.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L250](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L250)


- File: solidity/contracts/core/USDA.sol
```
 
Line: 262          _gonsPerFragment = _totalGons / _totalSupply
```
 Should be unchecked - _totalGons / _totalSupply.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L262](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L262)


- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 104          _gonsPerFragment = _totalGons / _totalSupply
```
 Should be unchecked - _totalGons / _totalSupply.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L104](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L104)


- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 196          uint256 _value = _gonValue / _gonsPerFragment
```
 Should be unchecked - _gonValue / _gonsPerFragment.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L196](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L196)


- File: solidity/contracts/utils/UFragments.sol
```
 
Line: 245          uint256 _value = _gonValue / _gonsPerFragment
```
 Should be unchecked - _gonValue / _gonsPerFragment.
[https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L245](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L245)


</details>

# 
