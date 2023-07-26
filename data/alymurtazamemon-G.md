# Amphora Protocol - Gas Optimization Report

## Table Of Content

| Number                                                                                           | Issue                                                                            | Instances |
| :----------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------- | --------: |
| [G-01](#g-01---functions-guaranteed-to-revert-when-called-by-normal-users-can-be-marked-payable) | Functions guaranteed to revert when called by normal users can be marked payable |        50 |
| [G-02](#g-02---multiple-reads-of-storage-variable-which-can-be-cached-to-save-users-gas-cost)    | Multiple reads of `storage` variable which can be cached to save users gas cost  |         4 |
| [G-03](#g-03---variables-contains-keccak256-expression-should-be-immutable)                      | variables contains `keccak256` expression should be `immutable`                  |         1 |
| [G-04](#g-04---variables-only-write-on-deployment-should-be-immutable-to-save-users-gas)         | Variables only write on deployment should be `immutable` to save users gas       |         1 |
| [G-05](#g-05---re-order-the-modifiers-to-save-users-gas-cost)                                    | Re-order the modifiers to save users gas cost                                    |         5 |
| [G-06](#g-06---check-conditions-should-be-at-the-top-of-the-functions)                           | `check` conditions should be at the top of the functions                         |         1 |
| [G-07](#g-07---use-unchecked-where-possible-to-save-users-gas)                                   | Use unchecked where possible to save users gas                                   |         1 |

### [G-01] - Functions guaranteed to revert when called by normal users can be marked payable.

**Details**

It is advisable to always use payable keyword with functions which are only limited to owner or any other authority. This can save other users gas cost by deducting less execution cost because compiler will then not execute these opcodes CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which it does when payable is missing.

This saves 24 unit of gas per call excluded deployment cost.

`50 instances * 24 gas per call = 1200 gas units, unauthorized users can waste`, we can save just by adding a single keyword.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[AMPHClaimer.sol - Line 119](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L119)

| Calculation Type | Before | After | Gas Saved |
| :--------------- | :----- | :---- | --------: |
| Avg              | 4637   | 4613  |        24 |

```diff
-   function changeVaultController(address _newVaultController) external override onlyOwner {
+   function changeVaultController(address _newVaultController) external payable override onlyOwner {
```

Similarly for all these functions, users will save 24 gas per call just adding `payable` keyword.

[AMPHClaimer.sol - Line 128](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L128)

[AMPHClaimer.sol - Line 136](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L136)

[AMPHClaimer.sol - Line 144](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L144)

[USDA.sol - Line 63](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L63)

[USDA.sol - Line 161](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L161)

[USDA.sol - Line 182](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L182)

[USDA.sol - Line 212](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L212)

[USDA.sol - Line 224](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L224)

[USDA.sol - Line 231](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L231)

[USDA.sol - Line 239](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L239)

[USDA.sol - Line 253](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L253)

[USDA.sol - Line 278](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L278)

[USDA.sol - Line 287](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L287)

[USDA.sol - Line 297](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L297)

[Vault.sol - Line 89](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L89)

[Vault.sol - Line 117](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L117)

[Vault.sol - Line 139](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L139)

[Vault.sol - Line 164](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L164)

[Vault.sol - Line 284](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L284)

[Vault.sol - Line 293](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L293)

[Vault.sol - Line 305](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L305)

[VaultController.sol - Line 294](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L294)

[VaultController.sol - Line 299](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L299)

[VaultController.sol - Line 305](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L305)

[VaultController.sol - Line 312](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L312)

[VaultController.sol - Line 319](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L319)

[VaultController.sol - Line 331](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L331)

[VaultController.sol - Line 390](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L390)

[VaultController.sol - Line 416](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L416)

[VaultController.sol - Line 415](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L415)

[VaultController.sol - Line 435](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L435)

[GovernorCharlie.sol - Line 567](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L567)

[GovernorCharlie.sol - Line 575](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L575)

[GovernorCharlie.sol - Line 583](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L583)

[GovernorCharlie.sol - Line 594](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L594)

[GovernorCharlie.sol - Line 605](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L605)

[GovernorCharlie.sol - Line 616](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L616)

[GovernorCharlie.sol - Line 627](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L627)

[GovernorCharlie.sol - Line 638](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L638)

[GovernorCharlie.sol - Line 649](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L649)

[GovernorCharlie.sol - Line 660](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L660)

[GovernorCharlie.sol - Line 673](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L673)

[GovernorCharlie.sol - Line 684](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L684)

[GovernorCharlie.sol - Line 695](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L695)

[GovernorCharlie.sol - Line 706](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L706)

[CurveMaster.sol - Line 31](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L31)

[CurveMaster.sol - Line 41](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L41)

[CurveMaster.sol - Line 52](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L52)

[ChainlinkOracleRelay.sol - Line 65](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L65)

[UFragments.sol - Line 111](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L111)

## </details>

### [G-02] - Multiple reads of `storage` variable which can be cached to save users gas cost.

**Details**

SLOAD (is the opcode used of read storage variable) cost 100 units of gas and MLOAD & MSTORE (are the opcodes used to read and write to memory respectivily) cost 3, 3 units of gas respectivily.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[USDA.sol - Lines 48 - 53](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L48-L53)

```diff
modifier paysInterest() {
+   uint256 length_ = _vaultControllers.length();
-   for (uint256 _i; _i < _vaultControllers.length(); ) {
+   for (uint256 _i; _i < length_; ) {
        IVaultController(_vaultControllers.at(_i)).calculateInterest();
        unchecked {
            // * @audit gas - ++i
            _i++;
        }
    }
    _;
}
```

[USDA.sol - Line 132](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L132)

```diff
function withdrawAll() external override returns (uint256 _susdWithdrawn) {
    uint256 _balance = this.balanceOf(_msgSender());
+   uint256 reserveAmount_ = reserveAmount;
-   _susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance;
+   _susdWithdrawn = _balance > reserveAmount_ ? reserveAmount_ : _balance;
    _withdraw(_susdWithdrawn, _msgSender());
}
```

[USDA.sol - 142](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L142)

```diff
function withdrawAllTo(
    address _target
) external override returns (uint256 _susdWithdrawn) {
    uint256 _balance = this.balanceOf(_msgSender());
+   uint256 reserveAmount_ = reserveAmount;
-   _susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance;
+   _susdWithdrawn = _balance > reserveAmount_ ? reserveAmount_ : _balance;
    _withdraw(_susdWithdrawn, _target);
}
```

[USDA.sol - 260 - 263](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L260-L263)

```diff
function _donation(uint256 _amount) internal {
+   uint256 totalSupply_ = _totalSupply;
-   _totalSupply += _amount;
+   _totalSupply = totalSupply_ + _amount;

-   if (_totalSupply > MAX_SUPPLY) _totalSupply = MAX_SUPPLY;
+   uint256 MAX_SUPPLY_ = MAX_SUPPLY;
+   if (totalSupply_ > MAX_SUPPLY_) _totalSupply = MAX_SUPPLY_;

+   totalSupply_ = _totalSupply;
-   _gonsPerFragment = _totalGons / _totalSupply;
+   _gonsPerFragment = _totalGons / totalSupply_;

-   emit Donation(_msgSender(), _amount, _totalSupply);
+   emit Donation(_msgSender(), _amount, totalSupply_);
}
```

</details>

### [G-03] - variables contains `keccak256` expression should be `immutable`.

**Details**

Expressions are not constant they execute everytime the variable calls, using them with `immutable` variables will only compute them on deployment time.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[USDA.sol - Line 21](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L21)

```diff
-   bytes32 public constant VAULT_CONTROLLER_ROLE = keccak256("VAULT_CONTROLLER");
+   bytes32 public immutable VAULT_CONTROLLER_ROLE;
+   constructor {
+       VAULT_CONTROLLER_ROLE = keccak256("VAULT_CONTROLLER");
```

</details>

### [G-04] - Variables only write on deployment should be `immutable` to save users gas.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[USDA.sol - Line 26](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L26)

```diff
-   IERC20 public sUSD;
+   IERC20 public immutable sUSD;
```

## </details>

### [G-05] - Re-order the modifiers to save users gas cost.

**Details**

In the `USDA` contract's `_deposit` function, `whenNotPaused` modifier is used after `paysInterest`. This will waste users gas in executing `paysInterest` function when functions will be `paused`.

Re-order them to save users gas.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[USDA.sol - Line 101](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L101)

```diff
-   function _deposit(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
+   function _deposit(uint256 _susdAmount, address _target) internal whenNotPaused paysInterest {
```

[USDA.sol - Line 147](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L147)

```diff
-   function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
+   function _withdraw(uint256 _susdAmount, address _target) internal whenNotPaused paysInterest {
```

[USDA.sol - Line 161](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L161)

```diff
-   function mint(uint256 _susdAmount) external override paysInterest onlyOwner {
+   function mint(uint256 _susdAmount) external override onlyOwner paysInterest {
```

[USDA.sol - Line 182](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L182)

```diff
-   function burn(uint256 _susdAmount) external override paysInterest onlyOwner {
+   function burn(uint256 _susdAmount) external override onlyOwner paysInterest {
```

[USDA.sol - Line 202](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L202)

```diff
-   function donate(uint256 _susdAmount) external override paysInterest whenNotPaused {
+   function donate(uint256 _susdAmount) external override whenNotPaused paysInterest {
```

## </details>

### [G-06] - `check` conditions should be at the top of the functions.

**Details**

Add the `check` conditions at the top of the functions save users gas cost, because then function will not execute other things before it when it will revert later on.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[USDA.sol - Lines 148 - 150](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L148-L150)

These all conditions should be in their external functions.

```solidity
    if (reserveAmount == 0) revert USDA_EmptyReserve();
    if (_susdAmount == 0) revert USDA_ZeroAmount();
    if (_susdAmount > this.balanceOf(_msgSender())) revert USDA_InsufficientFunds();
```

</details>

### [G-07] - Use unchecked where possible to save users gas.

**Details**

When unchecked is used variable then compiler do not execute opcodes which check over/underflow errors, it can be risky some times but we should use it when we are sure it will not exceed the limits.

And here we have a huge limit for it.

<details>
  <summary>Spotted Findings</summary>
  <p></p>

[USDA.sol - Line 170 - 174](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L170-L174)

```diff
-   _gonBalances[_target] += _amount * __gonsPerFragment;
+   unchecked { _gonBalances[_target] += _amount * __gonsPerFragment; }
-   _totalSupply += _amount;
+   unchecked { _totalSupply += _amount; }
-   _totalGons += _amount * __gonsPerFragment;
+   unchecked { _totalGons += _amount * __gonsPerFragment; }
```

</details>