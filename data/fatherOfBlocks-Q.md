**ChainlinkOracleRelay.sol**
- L40/41/42 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0.


**TriCrypto2Oracle.sol**
- L32/34/35/36 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0.


**ChainlinkStalePriceLib.sol**
- L12/14 - Be careful because the function latestRoundData() returns 8 decimal places, this could be a problem.
https://stackoverflow.com/questions/72244034/how-can-i-convert-the-returned-value-from-latestrounddata


**ThreeLines0_100.sol**
- L246 - A division is made by the input _run, this should be validated previously because _run could be == 0 and revert without handling it.


**UniswapV3OracleRelay.sol**
- L42/43/44/50/51 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0.


**UniswapV3TokenOracleRelay.sol**
- L25 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0.


**CbEthEthOracle.sol**
- L29/31/32 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0.


**UFragments.sol**
- L328 - abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
If all arguments are strings and or bytes, bytes.concat() should be used instead.


**WUSDA.sol**
- L49 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0.

- L246 - A division is made by the _totalUsdaSupply input, this should be previously validated because _totalUsdaSupply could be == 0 and reverse.


**AMPHClainer.sol**
- L62/63/64 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0.


**VaultController.sol**
- L93/102/110 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0.


**GovernorCharlie.sol**
- L347/512 - abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
If all arguments are strings and or bytes, bytes.concat() should be used instead.
