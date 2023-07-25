# [L-01] USDA `withdrawAll` doesn’t take pending interest into account leaving user's interest unclaimed
USDA `withdrawAll` and `withdrawAllTo` doesn't really withdraw all of user balance. Since it calculate withdrawal balance before doing interest accrual, accrued interest won't be reflected in the calculated balance, leaving interest unclaimed.
```
  function withdrawAll() external override returns (uint256 _susdWithdrawn) {
    uint256 _balance = this.balanceOf(_msgSender());
    _susdWithdrawn = _balance > reserveAmount ? reserveAmount : _balance; // calculate withdrawal balance
    _withdraw(_susdWithdrawn, _msgSender()); // accrue interest after balance is calculated
  }

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