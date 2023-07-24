# Gas Optimizations Report

This report focuses on Amphora Protocol contest, in context of various improvements that can be made in terms of gas cost.

Some of the opportunities identified for improving gas efficiency throughout the codebase of Amphora protocol are categorised into 05 main areas; with further multiple instances in each of the category.

# Summary
[G-01] Use hardcode address instead address(this) (19 Instances)
[G-02] Use != 0 instead of > 0 for unsigned integer comparison (09 Instances)
[G-03] Public functions to external instead (09 Instances)
[G 04] String literals passed to abi.encode()/abi.encodePacked() should not be split by commas (10 Instances)
[G-05] It's cheaper to declare the variable outside the loop (13 Instances)



# [G-01] Use hardcode address instead address(this) (19 Instances)

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address.

Link to the code:
1.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L107
2.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L335
3.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L509
4.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L716
5.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L92
6.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L177
7.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L182
8.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L191
9.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L245
10.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L257
11.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L275
12.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L103
13.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L206
14.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L215
15.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L57
16.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L105
17.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L169
18.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L166
19.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L215





# [G-02] Use != 0 instead of > 0 for unsigned integer comparison (09 Instances)
	
Link to the Code:
1.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L77
2.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L80
3.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L558
4.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L660
5.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L206
6.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L223
7.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L224
8.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L276
9.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L210


# [G-03] Public functions to external instead (09 Instances)

Functions with public visibility, if not called within the contract needed to be changed external.

Link to the Code:
1.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L60
2.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L51
3.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol#L22
4.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L20
5.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L278
6.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L988
7.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L583
8.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L594
9.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L153


# [G 04] String literals passed to abi.encode()/abi.encodePacked() should not be split by commas (10 Instances)

String literals can be split into multiple parts and still be considered as a single string literal.
EACH new comma costs 21 gas due to stack operations and separate MSTOREs.
	
Link to the Code:
1.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L169
2.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L327
3.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L328
4.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L262
5.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L299
6.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L337
7.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L405
8.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L509
9.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L510
10.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L512


# [G-05] It's cheaper to declare the variable outside the loop (13 Instances)
Declaring a variable inside a loop result in variable being redeclared during each loop iteration which consume higher gas.
The variable gets reallocated when declared outside loop making it more gas efficient.

Link to the Code:
1.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L874
2.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L876
3.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L879
4.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L882
5.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L902
6.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L904
7.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L907
8.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L910
9.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L996
10.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L190
11.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L191
12.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L256
13.	https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L257
 
