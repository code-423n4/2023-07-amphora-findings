1 . Target : https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol

Bug Location:
The problematic line is within the _calculate function, specifically here:

_amphForThisTurn = ((_rate * _tempAmountReceived) / 1e12) / 1e6;


Bug Impact:
Due to this rounding error, the _amphForThisTurn value is not calculated accurately. This can lead to various problems, including incorrect distribution of AMPH tokens and potential loss of funds. The issue may result in excessive or insufficient amounts of AMPH tokens being minted when exchanging CRV tokens.

Proposed Fix:
To address the bug, the calculation in the _calculate function needs to be revised. One potential solution is to avoid using a division operation and instead perform the calculations using only multiplication:

_amphForThisTurn = (_rate * _tempAmountReceived) / 1e18;


By changing the division factor from 1e12 to 1e18, we can eliminate the rounding errors and accurately calculate the _amphForThisTurn value.











2 . TArget : https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol

Inheritance Order: The contract extends multiple other contracts and interfaces (Pausable, UFragments, IUSDA, ExponentialNoError, Roles). The inheritance order should be carefully considered to avoid potential conflicts between the inherited functions and modifiers.

Use of onlyPauser Modifier: The onlyPauser modifier is designed to check if the sender is the pauser address. However, it does not handle the case when pauser is not set. In this scenario, the contract will revert since address(pauser) will return zero address (address(0)). A check should be added to handle the case when pauser is not set.

Inconsistent Modifier Name: The modifier paysInterest is used to call the calculateInterest function of the IVaultController for each vault controller. However, the name of the modifier suggests that it should be paying interest, which is misleading. It would be better to rename the modifier to something like calculateAndPayInterest.

Possible Division by Zero: The reserveRatio function calculates _e18reserveRatio using the formula (reserveAmount * EXP_SCALE) / _totalSupply. There is a risk of division by zero if _totalSupply is zero. Proper checks and handling should be implemented to prevent this scenario.

Missing Mint and Burn Events: The _mint and _burn functions, which are used to mint and burn USDA tokens, respectively, do not emit Transfer events. It is standard practice to emit these events for transparency and compatibility with various tools.

Vulnerable paysInterest Modifier: The paysInterest modifier loops through all vault controllers and calls their calculateInterest functions. Depending on the number of vault controllers, this operation can lead to potential out-of-gas issues when the number of vault controllers is large. Consider refactoring this approach to a more gas-efficient solution.

Use of unchecked: The unchecked keyword is used in the loop inside the paysInterest modifier. While it might not cause issues for the current version of Solidity, it is a deprecated keyword, and its use may cause problems in future Solidity versions. It's better to avoid its use.





3 . Target : https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol

- In the transfer, transferAll, transferFrom, and transferAllFrom functions, the _gonValue variable is calculated as _value * _gonsPerFragment. This multiplication could result in an arithmetic overflow if _gonsPerFragment is large. To prevent this, we need to ensure that _value * _gonsPerFragment does not exceed MAX_UINT256.

Suggested Fix:
Change the _gonValue calculation to:

uint256 _gonValue = _value * _gonsPerFragment / (10 ** DECIMALS);



-In the constructor, the _gonsPerFragment variable is calculated as _totalGons / _totalSupply. If _totalSupply is zero, this division could result in a runtime exception. Additionally, since _totalSupply is set to INITIAL_FRAGMENTS_SUPPLY, we should ensure that this value is never zero.

Suggested Fix:
Ensure that INITIAL_FRAGMENTS_SUPPLY is non-zero, and consider adding a check to prevent division by zero, although this should never happen if INITIAL_FRAGMENTS_SUPPLY is correctly set.

- The validRecipient modifier checks if the _to address is not address(0) or the contract itself. However, the function is used as a modifier and can be used with delegate calls. Since the modifier contains a revert statement, it can cause the calling contract to revert unexpectedly, leading to potential security issues.
Suggested Fix:
Replace the revert statement with a return value and handle the condition in the caller function. Modifiers with potential revert conditions should be avoided when using delegate calls.

- The validRecipient modifier checks if the _to address is not address(0) or the contract itself. However, the function is used as a modifier and can be used with delegate calls. Since the modifier contains a revert statement, it can cause the calling contract to revert unexpectedly, leading to potential security issues.

Suggested Fix:
Replace the revert statement with a return value and handle the condition in the caller function. Modifiers with potential revert conditions should be avoided when using delegate calls.

- Lack of Access Control on setMonetaryPolicy:
The setMonetaryPolicy function is marked as external and is only accessible to the contract owner. However, there is no access control mechanism to ensure that only the contract owner can call this function. This could potentially allow unauthorized users to change the monetary policy address.

Suggested Fix:
Add a modifier or require statement to ensure that only the contract owner can call the setMonetaryPolicy function.


4 . Target : https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol

-   _usdaToWUSDA: This function converts an amount of USDA tokens to an equivalent amount of wUSDA tokens. The calculation uses the formula: _wusdaAmount = (_usdaAmount * MAX_wUSDA_SUPPLY) / _totalUsdaSupply;. If _usdaAmount and _totalUsdaSupply are both large numbers, the multiplication may exceed the maximum value of a uint256, leading to an incorrect result.

-   _wUSDAToUSDA: This function converts an amount of wUSDA tokens back to an equivalent amount of USDA tokens. The calculation uses the formula: _usdaAmount = (_wusdaAmount * _totalUsdaSupply) / MAX_wUSDA_SUPPLY;. Similarly, if _wusdaAmount and _totalUsdaSupply are large, the multiplication may cause overflow and result in an incorrect value.

Suggested Fix:


Here's a revised version of the functions using fixed-point arithmetic:

function _usdaToWUSDA(uint256 _usdaAmount, uint256 _totalUsdaSupply) private pure returns (uint256 _wusdaAmount) {
    // Convert to fixed-point arithmetic with 18 decimal places
    uint256 fixedPointAmount = _usdaAmount * (10**18);
    _wusdaAmount = (fixedPointAmount * MAX_wUSDA_SUPPLY) / _totalUsdaSupply;
}

function _wUSDAToUSDA(uint256 _wusdaAmount, uint256 _totalUsdaSupply) private pure returns (uint256 _usdaAmount) {
    // Convert to fixed-point arithmetic with 18 decimal places
    uint256 fixedPointAmount = _wusdaAmount * (10**18);
    _usdaAmount = (fixedPointAmount * _totalUsdaSupply) / MAX_wUSDA_SUPPLY;
}


This  uses fixed-point arithmetic by multiplying the input amounts with 10^18 before performing the division, effectively keeping the precision during the calculations.





