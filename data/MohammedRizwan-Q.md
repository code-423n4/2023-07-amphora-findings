## Summary

### Low Risk Issues
|Number|Issue|Instances| |
|-|:-|:-:|:-:|
| [L&#x2011;01] | Violation of Checks, Effect and Interactions pattern in _deposit() | 1 |
| [L&#x2011;02] | Set fee limit for changeProtocolFee() | 1 |
| [L&#x2011;03] | draft-ERC20Permit.sol is deprecated by openzeppelin | 1 |
| [L&#x2011;04] | owner must not be address(0) as per EIP-2612 in permit() | 1 |
| [L&#x2011;05] | Violation of Checks, Effect and Interactions pattern in controllerTransfer() | 1 |



### [L&#x2011;01]  Violation of Checks, Effect and Interactions pattern in _deposit()
It is always recommended to follow Checks, Effect and Interactions(CEI) pattern in contracts to prevent re-entrancy attacks. However this is violated in _deposit() of USDA.sol contract.

There is 1 instance of this issue:

```Solidity
File: core/solidity/contracts/core/USDA.sol

  function _deposit(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
    if (_susdAmount == 0) revert USDA_ZeroAmount();
    sUSD.transferFrom(_msgSender(), address(this), _susdAmount);
    _mint(_target, _susdAmount);
    // Account for the susd received
    reserveAmount += _susdAmount;

    emit Deposit(_target, _susdAmount);
  }
```

### Recommended Mitigation steps

```Solidity

  function _deposit(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
    if (_susdAmount == 0) revert USDA_ZeroAmount();
+    reserveAmount += _susdAmount;
    sUSD.transferFrom(_msgSender(), address(this), _susdAmount);
    _mint(_target, _susdAmount);
    // Account for the susd received
-    reserveAmount += _susdAmount;

    emit Deposit(_target, _susdAmount);
  }
```

### [L&#x2011;02]  Set fee limit for changeProtocolFee()
In VaultController.sol, changeProtocolFee() is used to update the protocol fee. This function can only be accessed by owner of contract. However in a recent hack a malicious owner had set the fee to 100% causing severe loss to users. Therefore, to prevent such hacks it is recommended to set the fee limit.

There is 1 instance of this issue:

```Solidity
File: core/solidity/contracts/core/VaultController.sol

  function changeProtocolFee(uint192 _newProtocolFee) external override onlyOwner {
    if (_newProtocolFee >= 1e18) revert VaultController_FeeTooLarge();
    protocolFee = _newProtocolFee;
    emit NewProtocolFee(_newProtocolFee);
  }
```

### Recommended Mitigation Steps
Add validation check with fee limit at the start of changeProtocolFee().


### [L&#x2011;03]  draft-ERC20Permit.sol is deprecated by openzeppelin
In WUSDA.sol, draft-ERC20Permit.sol is used in contract but Openzeppelin has deprecated the use of draft-ERC20Permit.sol in v4.9.0 release. Check out the deprecations [here](https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.9.0).

There is [1](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/WUSDA.sol#L9) instance of this issue:

### Recommended Mitigation steps
Use [ERC20Permit.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/ERC20Permit.sol).

### [L&#x2011;04]  owner must not be address(0) as per EIP-2612 in permit()
Per EIP-2612, ```owner is not the zero address.``` must be validated otherwise the permit() function will revert.
Reference link: https://eips.ethereum.org/EIPS/eip-2612

In UFragments.sol, permit() is used as below,

```Solidity
File: core/solidity/contracts/utils/UFragments.sol

  function permit(
    address _owner,
    address _spender,
    uint256 _value,
    uint256 _deadline,
    uint8 _v,
    bytes32 _r,
    bytes32 _s
  ) public {
    require(block.timestamp <= _deadline);


    // Some code


```

### Recommended Mitigation steps

```Solidity

  function permit(
    address _owner,
    address _spender,
    uint256 _value,
    uint256 _deadline,
    uint8 _v,
    bytes32 _r,
    bytes32 _s
  ) public {
+   require(_owner != address(0), "invalid address");
    require(block.timestamp <= _deadline);


    // Some code


```

### [L&#x2011;05]  Violation of Checks, Effect and Interactions pattern in controllerTransfer()
It is always recommended to follow Checks, Effect and Interactions(CEI) pattern in contracts to prevent re-entrancy attacks. However this is violated in controllerTransfer() of Vault.sol contract.

There is 1 instance of this issue:

```Solidity
File: core/solidity/contracts/core/Vault.sol

  function controllerTransfer(address _token, address _to, uint256 _amount) external override onlyVaultController {
    SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_token), _to, _amount);
    balances[_token] -= _amount;
  }
```

### Recommended Mitigation steps

```Solidity
File: core/solidity/contracts/core/Vault.sol

  function controllerTransfer(address _token, address _to, uint256 _amount) external override onlyVaultController {
+   require(_token != address(0), "invalid address");
+   require(_amount != 0, "zero amount");
+   balances[_token] -= _amount;
    SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_token), _to, _amount);
-   balances[_token] -= _amount;
  }
```


