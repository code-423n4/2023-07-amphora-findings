**UFragments.sol**
- L39 - The LogRebase event is created, but it is never used, therefore an extra gas expense is generated in the deploy, it should be eliminated.


**TriCrypto2Oracle.sol**
- L12 - The TriCryptoOracle_ZeroAmount error is created, but it is never used, therefore an extra gas expense is generated in the deploy, it should be eliminated.


**IVaultDeployer.sol**
- L20 - The error VaultDeployer_OnlyVaultController is created, but it is never used, therefore an extra gas expense is generated in the deploy, it should be eliminated.
