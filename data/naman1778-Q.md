## [N-01] Use a modifier for access control

Consider using a modifier to implement access control instead of inlining the condition/requirement in the function’s body.

There are 3 instances of this issue in 2 files:

    File: VaultController.sol	

    527:  function _borrow(uint96 _id, uint192 _amount, address _target, bool _isUSDA) internal paysInterest whenNotPaused {
    528:    // grab the vault by id if part of our system. revert if not
    529:    IVault _vault = _getVault(_id);
    530:    // only the minter of the vault may borrow from their vault
    531:    if (_msgSender() != _vault.minter()) revert VaultController_OnlyMinter();

    1030: function modifyTotalDeposited(uint96 _vaultID, uint256 _amount, address _token, bool _increase) external override {
    1031:   if (_msgSender() != vaultIdVaultAddress[_vaultID]) revert VaultController_NotValidVault();

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol

    File: GovernorCharlie.sol	

    328: function executeTransaction(
    329:   address _target,
    330:    uint256 _value,
    331:    string memory _signature,
    332:    bytes memory _data,
    333:    uint256 _eta
    334:  ) external payable override {
    335: if (msg.sender != address(this)) revert GovernorCharlie_NotGovernorCharlie();

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

## [N-02] Contract files should define a locked compiler version

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

There are 25 instances of this issue in 25 files:

    File: VaultController.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol

    File: GovernorCharlie.sol	

    3: pragma solidity ^0.8.9;
 
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

    File: Vault.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol

    File: USDA.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol

    File: UFragments.sol	

    3: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol

    File: AMPHClaimer.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol

    File: WUSDA.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol

    File: AnchoredViewRelay.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol

    File: ThreeLines0_100.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol

    File: UniswapV3OracleRelay.sol	

    2: pragma solidity >=0.5.0 <0.9.0;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol

    File: CbEthEthOracle.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol
 
    File: ChainlinkOracleRelay.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol

    File: StableCurveLpOracle.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol

    File: TriCrypto2Oracle.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/TriCrypto2Oracle.sol

    File: GovernanceStructs.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/GovernanceStructs.sol

    File: CurveMaster.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol

    File: CTokenOracle.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol

    File: EthSafeStableCurveOracle.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol

    File: ChainlinkTokenOracleRelay.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol

    File: WstEthOracle.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/WstEthOracle.sol

    File: UniswapV3TokenOracleRelay.sol	

    2: pragma solidity >=0.5.0 <0.9.0;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3TokenOracleRelay.sol

    File: AmphoraProtocolToken.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol
  
    File: OracleRelay.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol

    File: VaultDeployer.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultDeployer.sol

    File: ChainlinkStalePriceLib.sol	

    2: pragma solidity ^0.8.9;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol

## [N-03] According to the syntax rules, use *=> mapping (* instead of *=> mapping(* using spaces as keyword

There are 2 instances of this issue in 2 files:

    File: GovernorCharlie.sol	

    77: mapping(uint256 => mapping(address => Receipt)) public proposalReceipts;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

    File: UFragments.sol	

    82: mapping(address => mapping(address => uint256)) private _allowedFragments;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol

## [N-04] Consider addings checks for signature malleability

Use OpenZeppelin’s *ECDSA* contract rather than calling *ecrecover()* directly

There are 2 instances of this issue in 2 files:

    File: GovernorCharlie.sol	

    517: address _signatory = ecrecover(_digest, _v, _r, _s);
    518: if (_signatory == address(0)) revert GovernorCharlie_InvalidSignature();
 
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

    File: UFragments.sol	

    333: require(_owner == ecrecover(_digest, _v, _r, _s));

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol

## [N-05] Contract Owner Has Too Many Privileges

The owner of the contracts has too many privileges relative to standard users. The consequence is disastrous if the contract owner’s private key has been compromised. And, in the event the key was lost or unrecoverable, no implementation upgrades and system parameter updates will ever be possible.

For a project this grand, it increases the likelihood that the owner will be targeted by an attacker, especially given the insufficient protection on sensitive owner private keys. The concentration of privileges creates a single point of failure; and, here are some of the incidents that could possibly transpire:

Transfer ownership and mess up with all the setter functions, hijacking the entire protocol.

Consider:

-- splitting privileges (e.g. via the multisig option) to ensure that no one address has excessive ownership of the system,
-- clearly documenting the functions and implementations the owner can change,
-- documenting the risks associated with privileged users and single points of failure, and
-- ensuring that users are aware of all the risks associated with the system.

