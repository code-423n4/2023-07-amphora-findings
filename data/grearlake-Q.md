# No support for token that have decimals > 18

Contract have mechanism that do not accept any token that have decimals > 18:

    if (_tokenDecimals > MAX_DECIMALS)
        revert VaultController_TooManyDecimals();

And MAX_DECIMALS is set to 18:

    /// @dev The max decimals allowed for a listed token
    uint8 public constant MAX_DECIMALS = 18;

But the problem is, there are lots of token that have decimals > 18. I beleive there should be support for token that have decimals > 18 and have mechanism to handle them