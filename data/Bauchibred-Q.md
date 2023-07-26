# Amphora Protocol QA Report

## **Table of Contents**

| Identifier | Issue                                                                              |
| ---------- | ---------------------------------------------------------------------------------- |
| QA-01      | Prevent underflows in the recovery of stranded tokens                              |
| QA-02      | The `if (_answer <= 0)` check for chainlink return is no longer needed             |
| QA-03      | A different oracle should be used to price stETH                                   |
| QA-04      | Unnecessary dual type casting adds to code complexity                              |
| QA-05      | WUSDA.sol: `mintFor()` should not be allowed to mint to the zero address           |
| QA-06      | Unnecessary calculation due to lack of check for decimal precision                 |
| QA-07      | `_migrateCollateralsFrom()` could be made more effective                           |
| QA-08      | `TriCrypto2Oracle::_get()` should be made more similar to its Vyper implementation |
| QA-09      | Unsafe typecasts                                                                   |
| QA-10      | Functions including boundaries should implement sanity checks                      |
| QA-11      | No protection against division/multiplication by zero                              |
| QA-12      | Fix typos                                                                          |
| QA-13      | Underflow in `withdraw()` and `withdrawTo()` not protected against                 |
| QA-14      | More measures should be taken to take care of any stranded tokens in the contract  |
| QA-15      | Lack of consistency in `_mint()` and `_burn()` functions                           |

## QA-01 - Prevent underflows in the recovery of stranded tokens

### Vulnerability Details

The current implementation of the contract includes a function, `recoverDust`, which is designed to manage the risk of tokens accidentally being sent to the contract. The function transfers out any surplus sUSD tokens after accounting for a reserved amount.

However, this function does not adequately account for a possible underflow scenario. In its current implementation, if the balance of the sUSD tokens in the contract is less than or equal to the `reserveAmount`, an underflow will occur, causing an incorrect value to be assigned to the `_amount` variable. This can lead to unintended consequences and could potentially render the tokens in the contract inaccessible.

Here's the current implementation:

```solidity
function recoverDust(address _to) external onlyOwner {
    uint256 _amount = sUSD.balanceOf(address(this)) - reserveAmount;
    require(sUSD.transfer(_to, _amount), "Transfer failed");
}
```

### Recommendation

To mitigate this issue, a check should be included in the function to ensure that the balance of sUSD tokens in the contract is greater than `reserveAmount`. This will prevent an underflow scenario. Furthermore, a descriptive error message should be provided to clearly communicate why a dust recovery operation failed, if that's the case.

The improved function would look something like this:

```solidity
function recoverDust(address _to) external onlyOwner {
    uint256 balance = sUSD.balanceOf(address(this));
    require(balance > reserveAmount, "Insufficient balance to recover dust");
    uint256 _amount = balance - reserveAmount;
    require(sUSD.transfer(_to, _amount), "Transfer failed");
}
```

In this revised function, a requirement checks that the balance of sUSD tokens is greater than `reserveAmount`. If this condition is not met, it throws an error stating "Insufficient balance to recover dust" and the function execution is halted. This change effectively prevents the underflow scenario, making the `recoverDust` function more robust and safe.

## QA-02 - The `if (_answer <= 0) ` check for chainlink returned is no longer needed

### Vulnerability Details Impact

Take a look at the [`getCurrentPrice()`](https://github.com/code-423n4/2023-07-amphora/blob/179384321c36b669f48bc0485bbc1f807fba8fac/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol#L11-L14) function from the `ChainlinkStalePriceLib` library

```solidity
function getCurrentPrice(AggregatorV2V3Interface _aggregator) internal view returns (uint256 _price) {
  (, int256 _answer,,,) = _aggregator.latestRoundData();
  //@audit
  if (_answer <= 0) revert Chainlink_NegativePrice();
  _price = uint256(_answer);
}
```

The contract checks whether the `_answer` returned by the `_aggregator.latestRoundData()` is less than or equal to zero, and if so, reverts the transaction. However, due to Chainlink's inbuilt circuit breakers, this condition should never be true since the prices can't go to 0

### Recommendation

The check for negative prices may be safely removed if the contract relies solely on Chainlink oracles for price data, as these oracles should never return a negative price. By removing unnecessary checks, the contract can be simplified, and the cost for calling the function could potentially be reduced.

## QA-03 A different oracle should be used to price stETH

### Vulnerability Details

In the `WstEthOracle` contract, the method used for calculating the current price of the stETH token relies on the stETH/ETH Chainlink Oracle, as suggested by the [scripts](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/scripts/CreateOracles.sol#L217-L219) This oracle operates with a _24-hour heartbeat and a 2% deviation threshold,_ leading to a significant time delay and possible discrepancies in price.

The central area of concern lies within the `_get` function where the stETH price is determined:

```solidity
function _get() internal view returns (uint256 _value) {
    uint256 _stETHPrice = STETH_ANCHORED_VIEW_UNDERLYING.peekValue();
    uint256 _stETHPerWstEth = WSTETH.stEthPerToken();
    _value = (_stETHPrice * _stETHPerWstEth) / 1e18;
}
```

In this function, the `_stETHPrice` is derived from the `STETH_ANCHORED_VIEW_UNDERLYING` Oracle. The nature of the stETH/ETH Chainlink Oracle means the on-chain price can exhibit significant differences from the actual real-world stETH price. This is due to the 24-hour heartbeat and the 2% deviation threshold of the oracle. If the value returned from the `_get` function is then used in a minting operation or any operation that determines an exchange rate, it could result in a potential loss of funds due to the deviation from the actual stETH price.

### Recommendation

The stETH/ETH Chainlink Oracle could be replaced with the stETH/USD Chainlink Oracle. The latter has a 1-hour heartbeat and a 1% deviation threshold, thus ensuring the on-chain stETH price is updated more frequently and accurately.

NB: This issue was submitted as part of QA after extensive discussion with sponsors on how this is protocol design, but I argue that if there is a chance to implement stETH/USD feed it should be used instead.

## QA-04 - Unnecessary dual type casting adds to code complexity

### Vulnerability Details

Refer to `ChainlinkOracleRelay.sol`

```soldity
  /// @notice Returns last second value of the oracle
  /// @dev    It does not revert if price is stale
  /// @return _value The last second value of the oracle
  function _getLastSecond() private view returns (uint256 _value) {
    uint256 _latest = ChainlinkStalePriceLib.getCurrentPrice(_AGGREGATOR);
    // @audit QA double casting, check lightchaser axelar
    _value = (uint256(_latest) * MULTIPLY) / DIVIDE;
  }
}
```

Employing dual type casting in Solidity contracts should generally be circumvented to prevent unforeseen repercussions and to ensure accurate portrayal of data. Consecutive type casting might result in unexpected truncation, rounding off errors, or loss of precision, potentially posing a threat to the functionality and reliability of the contract. Moreover, such dual type casting could hamper the readability of the code and make it more difficult to maintain, thereby raising the potential for errors and misconceptions during development and debugging phases. It's advisable for developers to employ suitable data types and shun unnecessary or extensive type casting, thereby reinforcing more dependable and secure contract execution.

### Recommendation

Implement safe measures while casting

## QA-05 - WUSDA.sol: `mintFor()` should not be allowed to mint to the zero address

### Vulnerability Details

The contract contains a function `mintFor(address _to, uint256 _wusdaAmount)` that mints `wUSDA` tokens and assigns them to the address `_to`. There is a lack of proper checks for the zero address (`0x0`) which can lead to tokens being minted and assigned to the zero address. This can result in tokens being irretrievable and essentially burnt, altering the tokenomics unpredictably.

```solidity
function mintFor(address _to, uint256 _wusdaAmount) external override returns (uint256 _usdaAmount) {
    // ... Some code ...

    _mint(_to, _wusdaAmount);  // This call allows minting to zero address.

    // ... Some code ...
}
```

The `_mint()` function being an internal function is also devoid of checks that would prevent such a situation.

```solidity
function _mint(address _to, uint256 _wusdaAmount) internal {
    // ... Some code ...

    balances[_to] += _wusdaAmount; // This operation allows updating balance of zero address.

    // ... Some code ...
}
```

### Recommendation

The contract should include checks to prevent token transfers to the zero address. This can be achieved by adding a `require()` statement to check if the `_to` address is not the zero address before proceeding with the mint operation.

Here's how the updated `mintFor()` and `_mint()` functions could look:

```solidity
function mintFor(address _to, uint256 _wusdaAmount) external override returns (uint256 _usdaAmount) {
    // Add a check to prevent minting to the zero address
    require(_to != address(0), "ERC20: mint to the zero address");

    // ... Rest of the code ...

    _mint(_to, _wusdaAmount);

    // ... Rest of the code ...
}
```

```solidity
function _mint(address _to, uint256 _wusdaAmount) internal {
    // ... Some code ...

    balances[_to] += _wusdaAmount; // This operation is now safe

    // ... Some code ...
}
```

By ensuring the `_to` address in `mintFor()` and `_mint()` is not the zero address, the contract will prevent irreversible loss of tokens and maintain the intended token supply.

## QA-06 - Unnecessary calculation due to lack of check for decimal precision

### Vulnerability Details

In the `UniswapV3OracleRelay` contract, the `getLastSeconds` function uses `_toBase18` function to adjust the Uniswap price to base 18. However, there is an oversight in the current implementation. The decimal conversion from Uniswap's price to Base 18 is always carried out irrespective of the token's decimals, which could lead to unnecessary calculations if the token's decimals are equal to 18.

Here is the relevant code snippet in the [UniswapV3OracleRelay contract](https://github.com/code-423n4/2023-07-amphora/blob/179384321c36b669f48bc0485bbc1f807fba8fac/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L84-L86):

```solidity
  function _getLastSeconds(uint32 _seconds) internal view returns (uint256 _price) {
    uint256 _uniswapPrice = _getPriceFromUniswap(_seconds, uint128(10 ** BASE_TOKEN_DECIMALS));
    //@audit-issue   check should be added to only query this when the decimals != 18
    _price = _toBase18(_uniswapPrice, QUOTE_TOKEN_DECIMALS);
  }
```

As seen from the code snippet, the function doesn't account for situations where the decimal count is already 18, leading to potential miscalculations.

### Recommendation

Introduce a condition to check the number of decimals of the token before invoking the `_toBase18` function.

## QA-07 - `_migrateCollateralsFrom()` could be made more effective

### Vulnerability Details

In `VaultController.sol` code , the `_migrateCollateralsFrom` aims to transfer collateral information from an old vault controller to the new one. During the process, the function checks if a `tokenId` for each of the provided `_tokenAddresses` exists in the old vault controller. If it doesn't (`_tokenId == 0`), the transaction is reverted with the `VaultController_WrongCollateralAddress` error.

This means if a single token address in the `_tokenAddresses` array does not exist in the old vault controller, the entire transaction reverts, regardless of the validity of the other addresses. Such a behavior is problematic for reasons such as:

1. **_Lack of Specificity_**: The transaction error does not provide any clarity on which specific token address caused the transaction to fail. This can cause inefficiencies and confusion for developers or system administrators trying to troubleshoot the issue.
2. **_All-or-Nothing Approach_**: This methodology can be seen as inefficient. If there's an error with a single token, it shouldn't halt the entire process for the other valid tokens.

### Recommendation

Instead of reverting with the `VaultController_WrongCollateralAddress` error, implement a way to specify which of the tokenAddresses exactly is erroing out

## QA-08 - `TriCrypto2Oracle::_get()` should be made more similar to it's vyper implementation

### Vulnerability Details

Take a look at [TriCrypto2Oracle.sol#L48-L84](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/periphery/oracles/TriCrypto2Oracle.sol#L48-L84)

```solidity
// @notice Calculated the price of 1 LP token
  /// @dev This function comes from the implementation in vyper that is on the bottom
  /// @return _maxPrice The current value
  function _get() internal view returns (uint256 _maxPrice) {
    uint256 _vp = TRI_CRYPTO.get_virtual_price();

    // Get the prices from chainlink and add 10 decimals
    uint256 _wbtcPrice = (WBTC_ORACLE_RELAY.peekValue());
    uint256 _ethPrice = (ETH_ORACLE_RELAY.peekValue());
    uint256 _usdtPrice = (USDT_ORACLE_RELAY.peekValue());

    uint256 _basePrices = (_wbtcPrice * _ethPrice * _usdtPrice);

    _maxPrice = (3 * _vp * FixedPointMathLib.cbrt(_basePrices)) / 1 ether;
    //@audit Q: Are there no instances where discount could be high to a point and make a difference? Isn't it better to just calculate the discount, set a reasonable limit and if it is lower than this it's ignored otherwise no?
    // removed discount since the % is so small that it doesn't make a difference
  }

  /*///////////////////////////////////////////////////////////////
                            VYPER IMPLEMENTATION
  //////////////////////////////////////////////////////////////*/

  // @external
  // @view
  // def lp_price() -> uint256:
  //     vp: uint256 = Tricrypto(POOL).virtual_price()
  //     p1: uint256 = convert(Chainlink(BTC_FEED).latestAnswer() * 10**10, uint256)
  //     p2: uint256 = convert(Chainlink(ETH_FEED).latestAnswer() * 10**10, uint256)
  //     max_price: uint256 = 3 * vp * self.cubic_root(p1 * p2) / 10**18
  //     # ((A/A0) * (gamma/gamma0)**2) ** (1/3)
  //     g: uint256 = Tricrypto(POOL).gamma() * 10**18 / GAMMA0
  //     a: uint256 = Tricrypto(POOL).A() * 10**18 / A0
  //     discount: uint256 = max(g**2 / 10**18 * a, 10**34)  # handle qbrt nonconvergence
  //     # if discount is small, we take an upper bound
  //     discount = self.cubic_root(discount) * DISCOUNT0 / 10**18
  //     max_price -= max_price * discount / 10**18
  //     return max_price
}

```

In the Solidity implementation of the `_get()` function, the original `discount` component used in the Vyper implementation has been entirely removed. This `discount` was part of the formula to calculate `_maxPrice` and was intended to provide a more dynamic and adaptable pricing mechanism.

The comment in the Solidity implementation indicates that the `discount` was deemed too small to make a difference and hence removed. However, it's crucial to understand that, in specific edge cases, such seemingly small discounts might aggregate to substantial amounts

Considering different market conditions and token volatilities, it may be unwise to entirely dismiss the `discount` component because it could potentially amount to a more considerable sum in certain scenarios.

### Recommendation

Instead of removing the `discount` component entirely, it would be more prudent to implement a minimal limit for it. If the `discount` is below this limit, it can be safely ignored. However, if it's above this threshold, it should be integrated into the `_maxPrice` calculation.

This strategy allows for the flexibility of the `discount` mechanism when it's indeed necessary while ignoring it when it's negligibly small. It provides a more robust implementation against different market conditions and potential edge cases, ensuring a more accurate and dynamic pricing mechanism for the LP token.

## QA-09 - Unsafe typecasts

### Vulnerability Details

In the `_payInterest()` function, an unsafe typecast from an unsigned integer to a signed integer is present, specifically with the reserve ratio value.

```solidity
    uint256 _ui18 = uint256(usda.reserveRatio());
    int256 _reserveRatio = int256(_ui18);
```

The issue lies in the fact that the cast from `uint256` to `int256` can result in an incorrect value if the reserve ratio is larger than `type(int256).max`, it easily leads to unexpected behavior in the `curveMaster.getValueAt()` function where `_reserveRatio` is used.

### Recommendation

Avoiding unsafe typecasts, especially when the inputs aren't strictly controlled. One solution could be to implement checks to ensure the value of `_ui18` (the reserve ratio) doesn't exceed the maximum possible value for `int256` before the typecast occurs.

## QA-10 - Functions including boundaries should implement sanity checks

### Vulnerability Details

The `vaultSummaries` designed to return a summary of vaults within a specified range, determined by the parameters `_start` and `_stop`. However, currently, the function does not validate if `_stop` is greater than `_start`. This missing sanity check could lead to an unexpected behavior if `_start` is larger than `_stop`, which would result in an array of zero length being created. This can lead to confusion, unexpected behavior, or even potential errors in calling functions if they are not designed to handle zero-length arrays.

In addition, it's worth noting that the code does set `_stop` to `vaultsMinted` if `_stop` is greater than `vaultsMinted`. However, this doesn't address the scenario where `_start` is larger than `_stop`.
Take a look at the fucntion:

```solidity
  function vaultSummaries(
    uint96 _start,
    uint96 _stop
  ) public view override returns (VaultSummary[] memory _vaultSummaries) {
    if (_stop > vaultsMinted) _stop = vaultsMinted;
    _vaultSummaries = new VaultSummary[](_stop - _start + 1);
    for (uint96 _i = _start; _i <= _stop;) {
      IVault _vault = _getVault(_i);
      uint256[] memory _tokenBalances = new uint256[](enabledTokens.length);

      for (uint256 _j; _j < enabledTokens.length;) {
        _tokenBalances[_j] = _vault.balances(enabledTokens[_j]);

        unchecked {
          ++_j;
        }
      }
      _vaultSummaries[_i - _start] =
        VaultSummary(_i, _peekVaultBorrowingPower(_vault), this.vaultLiability(_i), enabledTokens, _tokenBalances);

      unchecked {
        ++_i;
      }
    }
  }
```

### Recommendation

I recommend adding an additional check at the beginning of the function to validate that `_stop` is indeed greater than or equal to `_start`.

This can be done as follows:

```solidity
require(_stop >= _start, "Stop must be greater than start");
```

This ensures that the function can only be called with valid parameters, preventing potential logic errors or unexpected results.

## QA-11 - No protection against division/multiplication by zero

### Vulnerability Details

Look at `ChainlinkOracleRelay.sol::constructor`

```
// @audit QA add zero checks for mul/div, could easily be set to the 0x0 address
  constructor(
    address _underlying,
    address _feedAddress,
    uint256 _mul,
    uint256 _div,
    uint256 _stalePriceDelay
  ) OracleRelay(OracleType.Chainlink) {
    _AGGREGATOR = AggregatorV2V3Interface(_feedAddress);
    MULTIPLY = _mul;
    DIVIDE = _div;
    stalePriceDelay = _stalePriceDelay;

    _setUnderlying(_underlying);
  }

```

As seen the MULTIPLY & DIVIDE are further used for other arithmetic operations, but no checks implemented to make sure that they are not set to 0, which would easily lead to a `divisiion-by-zero` error

```solidity
  /// @notice Returns last second value of the oracle
  /// @dev    It does not revert if price is stale
  /// @return _value The last second value of the oracle
  function _getLastSecond() private view returns (uint256 _value) {
    uint256 _latest = ChainlinkStalePriceLib.getCurrentPrice(_AGGREGATOR);
    _value = (uint256(_latest) * MULTIPLY) / DIVIDE;
  }
```

### Recommendation

Add 0x0 checks against `_mul & _div` in `ChainlinkOracleRelay.sol::constructor`

## QA-12 - Fix Typos

### Vulnerability Details

1. Two instances from `AMPHClaimer.sol`
   Here is the NatSpec comment of the `claimAmph()` function

```solidity
  /// @notice Claims an amount of AMPH given a CVX and CRV quantity
  /// @param _vaultId The vault id that is claiming
  /// @param _cvxTotalRewards The max CVX amount to exchange from the sender
  //@audit typo change CVR to CRV
  /// @param _crvTotalRewards The max CVR amount to exchange from the sender
  /// @param _beneficiary The receiver of the AMPH rewards
  /// @return _cvxAmountToSend The amount of CVX that the treasury got
  /// @return _crvAmountToSend The amount of CRV that the treasury got
  /// @return _claimedAmph The amount of AMPH received by the beneficiary
```

See this line `  /// @param _crvTotalRewards The max CVR amount to exchange from the sender` As seen `CVR` should be `CRV` instead
Note that the same thing is applicable to the `claimable()` function too.

2. Two instances from `AMPHClaimer.sol`

Take a look at the the `_getVaultBorrowingPower()` function

```solidity
  /// @notice Returns the borrowing power of a vault
  /// @param _vault The vault to get the borrowing power of
  /// @return _borrowPower The borrowing power of the vault
  //solhint-disable-next-line code-complexity
  function _getVaultBorrowingPower(IVault _vault) private returns (uint192 _borrowPower) {
    //@audit QA typo, need to be registered instead, same at 50 lines from here
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
      if (_balance == 0) continue;
      // the raw price is simply the oracle price of the token
      uint192 _rawPrice = _safeu192(_collateral.oracle.currentValue());
      if (_rawPrice == 0) continue;
      // the token value is equal to the price * balance * tokenLTV
      uint192 _tokenValue = _safeu192(
        _truncate(_truncate(_balance * _collateral.ltv * _getPriceWithDecimals(_rawPrice, _collateral.decimals)))
      );
      // increase the ltv of the vault by the token value
      _borrowPower += _tokenValue;
    }
  }
```

See the first comment `    // loop over each registed token, adding the indivuduals ltv to the total ltv of the vault` _registed_ should instead be changed to _registerEd_
Note that the same thing is applicable to the `_peekVaultBorrowingPower()` function too.

3. One instance from the WUSDA.sol contract, at line 20 the below exist

```solidity
 *      For exusdae: 100K wUSDA => 1% of the usda market cap
```

Should instead be changed to:

```solidity
 *      For example  100K wUSDA => 1% of the usda market cap
```

Cause at present implemntation it's just a confusing comment.

### Recommendation

Fix the typos

## QA-13 - Underflow in `withdraw()` and `withdrawTo()` not protected against

### Vulnerability Details

The `withdraw()` and `withdrawTo()` functions in the USDA.sol contract allow users to withdraw sUSD by burning USDA tokens. Ideally, the contract should provide 1 sUSD for every 1 USDA burned. However, there is a potential underflow vulnerability in the `_withdraw()` function,

Specifically, the `_withdraw()` function decreases the `reserveAmount` by `_susdAmount`:
[Line 152 of USDA.sol](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/USDA.sol#L152)

```
reserveAmount -= _susdAmount;
```

However, there is no guarantee that `reserveAmount` is greater than or equal to `_susdAmount`. If `_susdAmount` is greater than `reserveAmount`, this operation could cause an underflow, not that this scenario is possible as a similar check is implemented in the sister functions, _i.e `withdrawAll()` and `withdrawAllTo()`_

### Recommendation

To mitigate this vulnerability, a safety check should be added to the `withdraw()` and `withdrawTo()` functions to see if `_susdAmount` is less than or equal to the `reserveAmount` before calling `_withdraw()`.

## QA-14 - More Measures Should be Taken to Take Care of any Stranded Tokens in the Contract

### Vulnerability Details

The current implementation of the contract includes a function, `recoverDust`, which has been put in place to manage the risk of tokens accidentally being sent to the contract. This function is designed to transfer out any surplus sUSD tokens, after accounting for the reserved amount.

However, this function has a limitation as it is hardcoded for the sUSD token only. This means, in the scenario where tokens other than sUSD are accidentally sent to the contract, there isn't a straightforward mechanism for recovering these tokens.

```solidity
function recoverDust(address _to) external onlyOwner {
    uint256 _amount = sUSD.balanceOf(address(this)) - reserveAmount;
    require(sUSD.transfer(_to, _amount), "Transfer failed");
}
```

### Recommendation

To address this, it is recommended to improve the `recoverDust` function by allowing it to handle other types of tokens. We should also ensure that the function can handle the edge case where the balance is equal to the `reserveAmount`.

Here is the revised `recoverDust` function, now named `recoverERC20` to reflect its generalized use case:

```solidity
function recoverERC20(IERC20 _token, address _to) external onlyOwner {
    uint256 balance = _token.balanceOf(address(this));
    if (address(_token) == address(sUSD)) {
        require(balance > reserveAmount, "No dust to recover");
        uint256 recoverableDust = balance - reserveAmount;
        require(_token.transfer(_to, recoverableDust), "Transfer failed");
    } else {
        require(_token.transfer(_to, balance), "Transfer failed");
    }
}
```

This function first checks whether the token to be recovered is the same as sUSD. If it is, it ensures that the balance is more than the `reserveAmount` and then proceeds to recover the balance less the `reserveAmount`. If the token is not sUSD, the function simply transfers the entire balance of the token to the specified address. This makes the function more robust and adaptable to different scenarios of accidental token deposits.

Moreover, it's important to ensure robust security practices are in place around the usage of the `onlyOwner` modifier, including thorough checks on the `_to` address to make sure it is not null or any other invalid address.

## QA-15 - Lack of Consistency in `_mint()` and `_burn()` functions

### Vulnerability Details

The `_mint()` and `_burn()` functions in the USDA.sol contract provide the core functionality of minting and burning USDA tokens respectively. However, there is a slight inconsistency between these two functions in how the total amount of gons (gons are used for higher precision tracking of token balances) is calculated.

In the `_mint()` function, the number of gons corresponding to the minted amount is calculated as:

```
_amount * __gonsPerFragment
```

While in the `_burn()` function, the calculation is slightly different, with the product being enclosed in parentheses:

```
(_amount * __gonsPerFragment)
```

Although this difference does not affect the function's correctness or security, it does introduce a small inconsistency that can potentially reduce the code's readability and could be confusing to future developers maintaining or reviewing the code.

### Recommendation

To improve the code's readability and maintain a consistent code style, it's recommended to make the style of the `_mint()` function's calculation match the `_burn()` function. This means enclosing the multiplication operation in parentheses, like so:

```
_totalGons += (_amount * __gonsPerFragment);
```

While this change does not affect the contract's behavior, it will make the code style more consistent and thus easier to read and understand. Maintaining a consistent code style is a good practice as it can help prevent misunderstandings and mistakes in code development and review.
