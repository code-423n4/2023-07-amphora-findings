## The return value of AddressSet is not checked

In the EnumerableSet.AddressSet library, both success and failure of adding will have a return value. But all calls do not check the return value, even if the addition fails or the deletion fails. functions can be executed normally

```solidity
function add(AddressSet storage set, address value) internal returns (bool) {
    return _add(set._inner, bytes32(uint256(uint160(value))));
}
function remove(AddressSet storage set, address value) internal returns (bool) {
    return _remove(set._inner, bytes32(uint256(uint160(value))));
}
```
unchecked return value
```solidty
core\solidity\contracts\core\USDA.sol:
  278    function addVaultController(address _vaultController) external onlyOwner {
  279:     _vaultControllers.add(_vaultController);
  280      _grantRole(VAULT_CONTROLLER_ROLE, _vaultController);

core\solidity\contracts\core\USDA.sol:
  287    function removeVaultController(address _vaultController) external onlyOwner {
  288:     _vaultControllers.remove(_vaultController);
  289      _revokeRole(VAULT_CONTROLLER_ROLE, _vaultController);

  297    function removeVaultControllerFromList(address _vaultController) external onlyOwner {
  298:     _vaultControllers.remove(_vaultController);
  299
```

It is recommended to check the return value of the function call