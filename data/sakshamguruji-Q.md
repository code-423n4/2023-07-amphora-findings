## Use Oracles instead of calculating price which might be inaccurate/Manipulated

Inside the WUSDA contract we calculate the price of WUSDA in terms of USDA and vice versa using the formula
`_wusdaAmount = (_usdaAmount * MAX_wUSDA_SUPPLY) / _totalUsdaSupply;`

(https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L247)

If the owner wishes , he can mint alot of USDA and increase the denominator of this calculation , resulting in
`_wusdaAmount` to be way smaller than what it should have been or to profit from it can burn alot of USDA and 
increase the WUSDA received.
After profiting from this he can mint/burn the amount back to keep the supply balanced.

Remediation:

Use oracles instead of calculating prices to avoid these kind of scenarios.

## Else condition not needed

The else condition here https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L374 is not needed since it is already checked in the if condition above.

## Add a check end > start

Inside this function https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L230 add a check `require(_end > _start)`

## USDA contract address can be set to any address by the owner

The function here https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L305 can set the USDA address by the owner , 
this holds great centralization risk since owner might assign any ERC20 as the USDA.

## Have a minimum value for the cap of the collateral

When adding an ERC20 , we add a deposit cap https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L367 , ensure a min value for this so that arbitrary low amount can't be set as the cap , that would be meaningless.

## Wrong Comments / Typos

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L444 -> should be ` _peekVaultBorrowingPower` instead of ` peekVaultBorrowingPower`

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L582 -> should be "repay" instead of "borrow"

## Users might repay for the wrong vault

If accidentally user gives an incorrect id here https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L568 , he may repay for the incorrect vault and lose funds.

## No need for check at L660

The check here https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L660 is not needed since tokensToLiquidate returned would never be 0.

## Event Misordering Possible

According to CEI , events should be emitted before the fund transfers. 

Example - > https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L561
