### [L-1] _withdraw in USDA does not follow the check effect interaction patterns:

it's better to _burn the _susdAmount first before calling sUSD.transfer which is an external call. If sUSD becomes a token that has beforeTokenTransfer then it has the potential to be subject to re-entrency.

```solidity
  function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
    if (reserveAmount == 0) revert USDA_EmptyReserve();
    if (_susdAmount == 0) revert USDA_ZeroAmount();
    if (_susdAmount > this.balanceOf(_msgSender())) revert USDA_InsufficientFunds();
    // Account for the susd withdrawn
    reserveAmount -= _susdAmount;
    sUSD.transfer(_target, _susdAmount);
    _burn(_msgSender(), _susdAmount);

    emit Withdraw(_target, _susdAmount);
  }
```
### [L-2] _withdraw in USDA call sUSD.transfer before calling _burn is not following CEI pattern.

```solidity
  function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
    if (reserveAmount == 0) revert USDA_EmptyReserve();
    if (_susdAmount == 0) revert USDA_ZeroAmount();
    if (_susdAmount > this.balanceOf(_msgSender())) revert USDA_InsufficientFunds();
    // Account for the susd withdrawn
    reserveAmount -= _susdAmount;
    sUSD.transfer(_target, _susdAmount);
    _burn(_msgSender(), _susdAmount);

    emit Withdraw(_target, _susdAmount);
  }
```
This adds risk on potential re-entrency if sUSD.transfer allows pre-transfer hooks

### [N-1] changeCvxRewardFee in AMPHClaimer does not have a cap. 

cvxRewardFee if mis-configured to beyond 100% would break many calculation like claimable due to overflow. Same with changeCrvRewardFee.

```solidity
  function changeCvxRewardFee(uint256 _newFee) external override onlyOwner {
    cvxRewardFee = _newFee;

    emit ChangedCvxRewardFee(_newFee);
  }
```

### [N-2] _propose in GovernorCharlie could use a struct to represent 4 arrays which are expected to be the same size, to remove length checks

```solidity
  function _propose(
    address[] memory _targets,
    uint256[] memory _values,
    string[] memory _signatures,
    bytes[] memory _calldatas,
    string memory _description,
    bool _emergency
  ) internal returns (uint256 _proposalId) {
    // Reject proposals before initiating as Governor
    if (quorumVotes == 0) revert GovernorCharlie_NotActive();
    // Allow addresses above proposal threshold and whitelisted addresses to propose
    if (amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender)) {
      revert GovernorCharlie_VotesBelowThreshold();
    }
    if (
      _targets.length != _values.length || _targets.length != _signatures.length || _targets.length != _calldatas.length
    ) revert GovernorCharlie_ArityMismatch();
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L159-L175

### [N-3]  make forceSetCurve a 1-time operation if it is only meant for deployment
```solidity
  /// @notice Special function that does not calculate interest, used for deployment
  function forceSetCurve(address _tokenAddress, address _curveAddress) external override onlyOwner {
    address _oldCurve = curves[_tokenAddress];
+++    require(_oldCurve == address(0));
    curves[_tokenAddress] = _curveAddress;

    emit CurveForceSet(_oldCurve, _tokenAddress, _curveAddress);
  }
```


### [N-4] Typo in setVaultController since it mis-name oldController as oldCurveAddress

```solidity
  function setVaultController(address _vaultMasterAddress) external override onlyOwner {
---    address _oldCurveAddress = vaultControllerAddress;
+++    address _oldVaultControllerAddress = vaultControllerAddress;
    vaultControllerAddress = _vaultMasterAddress;

---    emit VaultControllerSet(_oldCurveAddress, _vaultMasterAddress);
+++    emit VaultControllerSet(_oldVaultControllerAddress, _vaultMasterAddress);
  }
```

### [N-5] liquidation gas fee increases as enabledToken increases since external read on Vault increases linearly regardless if the liquidatedUser deposited the asset or not

liquidateVault would call `_getVaultBorrowingPower` at the end of execution to verify that the user is still not over-collateralized. However this function would loop over all the vault's balance on all enabledTokens, each is an external read on Vault, costing linear increasing gas even though the user may have just 1 asset deposited

```solidity
  function _getVaultBorrowingPower(IVault _vault) private returns (uint192 _borrowPower) {
    // loop over each registed token, adding the indivuduals ltv to the total ltv of the vault
    for (uint192 _i; _i < enabledTokens.length; ++_i) {
      CollateralInfo memory _collateral = tokenAddressCollateralInfo[enabledTokens[_i]];
      // if the ltv is 0, continue
      if (_collateral.ltv == 0) continue;
      // get the address of the token through the array of enabled tokens
      // note that index 0 of enabledTokens corresponds to a vaultId of 1, so we must subtract 1 from i to get the correct index
      address _tokenAddress = enabledTokens[_i];
      // the balance is the vault's token balance of the current collateral token in the loop
      uint256 _balance = _vault.balances(_tokenAddress);
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L866-L876