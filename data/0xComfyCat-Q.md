# [L-01] USDA `withdrawAll` doesn’t take pending interest into account leaving user's interest unclaimed

USDA `withdrawAll` and `withdrawAllTo` doesn't really withdraw all of user balance. Since it calculate withdrawal balance before doing interest accrual, accrued interest won't be reflected in the calculated balance, leaving interest unclaimed.
```
  function withdrawAll() external override returns (uint256 _susdWithdrawn) {
    uint256 _balance = this.balanceOf(_msgSender());
    _susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance; // @audit calculate withdrawal balance
    _withdraw(_susdWithdrawn, _msgSender()); // @audit accrue interest after balance is calculated
  }

  // @audit interest is accrued here after `_susdAmount` calculation
  function _withdraw(uint256 _susdAmount, address _target) internal paysInterest whenNotPaused {
    // ...
  }
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L126-L144
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L147

## Proof of Concept
Based on project's e2e test. The setup is that someone was borrowing USDA and there is enough sUSD reserve to withdraw. `testWithdrawAllDustLeft` test demonstrated that `withdrawAll` isn't able to withdraw accrued interest, leaving it unclaimed.
```
import {CommonE2EBase, IVault, console} from '@test/e2e/Common.sol';

contract WithdrawInterestPoC is CommonE2EBase {
  function setUp() public override {
    super.setUp();

    // Someone borrows
    bobVaultId = _mintVault(bob);
    vm.startPrank(bob);
    bobVault = IVault(vaultController.vaultIdVaultAddress(bobVaultId));
    weth.approve(address(bobVault), bobWETH);
    IVault(address(bobVault)).depositERC20(address(weth), bobWETH);
    uint256 _toBorrow = vaultController.vaultBorrowingPower(bobVaultId);
    vaultController.borrowUSDA(bobVaultId, uint192(_toBorrow / 2));
    vm.stopPrank();

    // Increase sUSD reserve
    vm.startPrank(andy);
    susd.approve(address(usdaToken), 100 ether);
    usdaToken.donate(100 ether);
    vm.stopPrank();
  }

  function testWithdrawAllDustLeft() public {
    uint256 _daveSUSDBefore = susd.balanceOf(dave);

    // User deposit sUSD, receive USDA
    _depositSUSD(dave, 100 ether);

    assertEq(susd.balanceOf(dave), _daveSUSDBefore - 100 ether);
    assertEq(usdaToken.balanceOf(dave), 100 ether);

    // Time travel
    vm.warp(block.timestamp + 1 days);

    // Withdraw
    vm.prank(dave);
    usdaToken.withdrawAll();
    // `withdrawAll` failed to account for pending interest thus user only received what they deposited
    assertEq(susd.balanceOf(dave), _daveSUSDBefore);
    // There is dust amount left unclaimed for user
    assertGt(usdaToken.balanceOf(dave), 0);
  }

  function testWithdrawAllNoDust() public {
    uint256 _daveSUSDBefore = susd.balanceOf(dave);

    // User deposit sUSD, receive USDA
    _depositSUSD(dave, 100 ether);

    assertEq(susd.balanceOf(dave), _daveSUSDBefore - 100 ether);
    assertEq(usdaToken.balanceOf(dave), 100 ether);

    // Time travel
    vm.warp(block.timestamp + 1 days);

    // This time we accrue interest before withdrawing
    vaultController.calculateInterest();

    // Withdraw
    vm.prank(dave);
    usdaToken.withdrawAll();
    // `withdrawAll` take pending interest into account so user received more than deposited
    assertGt(susd.balanceOf(dave), _daveSUSDBefore);
    // There is no dust left
    assertEq(usdaToken.balanceOf(dave), 0);
  }
}
```

## Recommended mitigation steps
`testWithdrawAllNoDust` test provided solution to this. By accruing interest before withdraw, user can withdraw all of their balance including interest, leaving nothing.
```
  // Apply `paysInterest` modifier at top level function to accrue before calculate balance
  function withdrawAll() external override paysInterest returns (uint256 _susdWithdrawn) {
    uint256 _balance = this.balanceOf(_msgSender());
    _susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance;
    _withdraw(_susdWithdrawn, _msgSender());
  }
```

# [L-02] USDA internal function `_mint` can bypass check performed by `validRecipient` used by transfer function

Functions that utilize `_mint` can send USDA to zero address or USDA contract itself.
Affected functions are
- USDA `depositTo` via `_target` parameter
- VaultController internal function `_borrow` via `_target` parameter

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L167-L178
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L93-L98
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L549-L552

## Proof of Concept
Extending `CommonE2EBase` contract. `testSendUSDAToAddressZero` test function showed that user can send USDA to address zero via `depositTo`
```
  function testSendUSDAToAddressZero() public {
    uint256 zeroAddressBalanceBefore = usdaToken.balanceOf(address(0));

    _dealSUSD(dave, 2 ether);

    vm.startPrank(dave);
    susd.approve(address(usdaToken), 1 ether);
    usdaToken.depositTo(1 ether, address(0));
    vm.stopPrank();

    assertEq(usdaToken.balanceOf(address(0)), zeroAddressBalanceBefore + 1 ether);

    // Sending via transfer is not possible
    vm.prank(dave);
    vm.expectRevert(abi.encodeWithSignature('UFragments_InvalidRecipient()'));
    usdaToken.transfer(address(0), 1 ether);
  }
```
## Recommended mitigation steps
Apply `validRecipient` modifier to `_mint` functions
```
function _mint(address _target, uint256 _amount) internal validRecipient(_target) {
```

# [L-03] `AMPHClaimer` CRV and CVX reward fee is not validated when being set

## Impact
Reward fee parameters set in constructor and setter functions `changeCvxRewardFee` and `changeCrvRewardFee` are not validated. If it is set to value greater than `_BASE` constant, contract would fail to function due to trying to take more fees than available balance.
```
  function _totalToFraction(uint256 _total, uint256 _fraction) internal pure returns (uint256 _amount) {
    if (_total == 0) return 0;
    _amount = (_total * _fraction) / _BASE; // @audit if `_fraction > _BASE` it would cause transfer to fail
  }
```

## Recommended mitigation steps
Validate that fee input always less than `_BASE` constant in constructor and setters
```
  constructor(
    address _vaultController,
    IERC20 _amph,
    IERC20 _cvx,
    IERC20 _crv,
    uint256 _cvxRewardFee,
    uint256 _crvRewardFee
  ) {
    ...
    if (_cvxRewardFee > _BASE || _crvRewardFee > _BASE) revert AMPHClaimer_InvalidFee();
    cvxRewardFee = _cvxRewardFee;
    crvRewardFee = _crvRewardFee;
  }

  function changeCvxRewardFee(uint256 _newFee) external override onlyOwner {
    if (_newFee > _BASE) revert AMPHClaimer_InvalidFee();
    cvxRewardFee = _newFee;

    emit ChangedCvxRewardFee(_newFee);
  }

  function changeCrvRewardFee(uint256 _newFee) external override onlyOwner {
    if (_newFee > _BASE) revert AMPHClaimer_InvalidFee();
    crvRewardFee = _newFee;

    emit ChangedCrvRewardFee(_newFee);
  }
```

# [L-04] `withdrawERC20` can be called with zero amount

## Impact
Even no damage could be done, it doesn't follow project's semantic e.g. `depositERC20` checks for zero amount input.

## Proof of Concept
Extending `CommonE2EBase` contract. The test showed that users can call `withdrawERC20` with zero as input.
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L117
```
  function testVaultWithdrawZeroAmount() public {
    // Bob mints vault
    uint96 bobsVaultId = _mintVault(bob);
    bobVault = IVault(vaultController.vaultIdVaultAddress(bobsVaultId));

    deal(address(weth), bob, 100 ether);
    vm.startPrank(bob);
    weth.approve(address(bobVault), 100 ether);
    bobVault.depositERC20(address(weth), 100 ether);

    // Able to withdraw with zero amount
    bobVault.withdrawERC20(address(weth), 0);
  }
```
## Recommended mitigation steps
Add the check
```
  function withdrawERC20(address _tokenAddress, uint256 _amount) external override onlyMinter {
    if (_amount == 0) revert Vault_AmountZero();
    ...
  }
```

# [L-05] View functions doesn't take borrower's pending interest into account

## Impact
View functions such as `vaultLiability`, `amountToSolvency`, `peekCheckVault`, etc. doesn't take borrower's pending interest into account. It would give protocol integrators extra effort to integrate with the protocol and possibility to do it wrong.

## Recommended mitigation steps
Create separate view functions that include pending interest in calculation.
```
  function vaultLiabilityWithPendingInterest(uint96 _id) external view override returns (uint192 _liability) {
    address _vaultAddress = vaultIdVaultAddress[_id];
    if (_vaultAddress == address(0)) revert VaultController_VaultDoesNotExist();
    IVault _vault = IVault(_vaultAddress);

    uint64 _timeDifference = uint64(block.timestamp) - interest.lastTime;
    uint256 _ui18 = uint256(usda.reserveRatio());
    int256 _reserveRatio = int256(_ui18);
    int256 _intCurveVal = curveMaster.getValueAt(address(0x00), _reserveRatio);
    uint192 _curveVal = _safeu192(uint256(_intCurveVal));
    uint192 _e18FactorIncrease = _safeu192(
      _truncate(
        _truncate((uint256(_timeDifference) * uint256(1e18) * uint256(_curveVal)) / (365 days + 6 hours))
          * uint256(interest.factor)
      )
    );
    _liability = _safeu192(_truncate(_vault.baseLiability() * (interest.factor + _e18FactorIncrease)));
  }
```