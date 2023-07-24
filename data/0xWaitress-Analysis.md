## UX - gon calculation is fine but lacks interface for user to fully burn USDA during repay

Quite inconvenient to call withdraw of USDA at VaultController after _gonPerFragment is changed. Since the same amount now can get more underlying out but there is no method for the user to withdraw based on totalGons.

Would be cool to consider having a post-gon calculation entry for user to burn, so they can fully burn their USDA amount.

## Liquidation is too strict for liquidator

2. Liquidation too strict since there is no close factor, but the user is only liquidated up to the point that he/she is/ is not “collateralized”; liquidation call would be easily reverted if any price feed changes slightly , and the final check would fail since liquidator would then "over-liquidate". Consider adding a "close-factor" such that liquidator can liquidate a certain amount of collateral as soon as the user enters liquidation.



### Time spent:
6 hours