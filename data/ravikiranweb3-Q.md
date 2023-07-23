1) VaultDeployer contract's deployVault can be called directly by passing a vault id. Such calls are not registered in the system as the id to address mapping is not stored in the vault controller.

It would be best if the deployVault can be called by Vault Controller only. Example, store the address of vault controller in vaultdeployer and check if the msg.sender is same as vault controller before allowing to setup the vault


2) _peekVaultBorrowingPower() and _getVaultBorrowingPower() are doing the same thing, seems redundant