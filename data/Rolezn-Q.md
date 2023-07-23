## QA Summary<a name="QA Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#low1-ierc20-approve-is-deprecated) | IERC20 `approve()` Is Deprecated | 3 |
| [LOW&#x2011;2](#low2-condition-will-not-revert-when-blocktimestamp-is--to-the-compared-variable) | Condition will not revert when `block.timestamp` is `==` to the compared variable | 2 |
| [LOW&#x2011;3](#low3-consider-the-case-where-totalsupply-is-0) | Consider the case where `totalsupply` is 0 | 6 |
| [LOW&#x2011;4](#low4-constant-decimal-values) | Constant decimal values | 13 |
| [LOW&#x2011;5](#low5-function-can-risk-gas-exhaustion-on-large-receipt-calls-due-to-multiple-mandatory-loops) | Function can risk gas exhaustion on large receipt calls due to multiple mandatory loops | 1 |
| [LOW&#x2011;6](#low6-minting-tokens-to-the-zero-address-should-be-avoided) | Minting tokens to the zero address should be avoided | 5 |
| [LOW&#x2011;7](#low7-the-contract-should-approve0-first) | The Contract Should `approve(0)` First | 1 |
| [LOW&#x2011;8](#low8-contracts-are-not-using-their-oz-upgradeable-counterparts) | Contracts are not using their OZ Upgradeable counterparts | 37 |
| [LOW&#x2011;9](#low9-no-storage-gap-for-upgradeable-contracts) | No Storage Gap For Upgradeable Contracts | 1 |
| [LOW&#x2011;10](#low10-use-safecast-to-safely-downcast-variables) | Use SafeCast to safely downcast variables | 5 |

Total: 74 contexts over 10 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#nc1-consider-adding-a-denylist) | Consider adding a deny-list | 3 |
| [NC&#x2011;2](#nc2-consistent-usage-of-require-vs-custom-error) | Consistent usage of require vs custom error | 4 |
| [NC&#x2011;3](#nc3-constant-redefined-elsewhere) | Constant redefined elsewhere | 1 |
| [NC&#x2011;4](#nc4-blocktimestamp-is-already-used-when-emitting-events-no-need-to-input-timestamp) | `block.timestamp` is already used when emitting events, no need to input timestamp | 1 |
| [NC&#x2011;5](#nc5-mint-events-missing-key-information) | Mint events missing key information | 1 |
| [NC&#x2011;6](#nc6-empty-bytes-check-is-missing) | Empty `bytes` check is missing | 7 |
| [NC&#x2011;7](#nc7-function-names-should-differ-to-make-the-code-more-readable) | Function names should differ to make the code more readable | 4 |
| [NC&#x2011;8](#nc8-function-must-not-be-longer-than-50-lines) | Function must not be longer than 50 lines | 2 |
| [NC&#x2011;9](#nc9-functions-that-alter-state-should-emit-events) | Functions that alter state should emit events | 4 |
| [NC&#x2011;10](#nc10-generate-perfect-code-headers-for-better-solidity-code-layout-and-readability) | Generate perfect code headers for better solidity code layout and readability | 9 |
| [NC&#x2011;11](#nc11-internal-functions-not-called-by-the-contract-should-be-removed) | `internal` functions not called by the contract should be removed | 1 |
| [NC&#x2011;12](#nc12-numeric-values-having-to-do-with-time-should-use-time-units-for-readability) | Numeric values having to do with time should use time units for readability | 1 |
| [NC&#x2011;13](#nc13-overly-complicated-arithmetic) | Overly complicated arithmetic | 2 |
| [NC&#x2011;14](#nc11-it-is-standard-for-all-external-and-public-functions-to-be-overridden-from-an-interface) | It is standard for all external and public functions to be overridden from an interface | 27 |
| [NC&#x2011;15](#nc15-unused-struct-definition) | Unused `struct` definition | 1 |
| [NC&#x2011;16](#nc4-use--instead-of-0x00-for-empty-bytes-to-avoid-triggering-extra-computation-on-transfers) | Use `` instead of `"0x00"` for empty bytes to avoid triggering extra computation on transfers | 1 |
| [NC&#x2011;17](#nc5-use-named-function-calls) | Use named function calls | 2 |
| [NC&#x2011;18](#nc18-use-smtchecker) | Use SMTChecker | 1 |
| [NC&#x2011;19](#nc19-cast-to-bytes-or-bytes32-for-clearer-semantic-meaning) | Cast to `bytes` or `bytes32` for clearer semantic meaning | 1 |

Total: 73 contexts over 19 issues

## Low Risk Issues

### <a href="#qa-summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> IERC20 `approve()` Is Deprecated

`approve()` is subject to a known front-running attack. It is deprecated in favor of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`. If only setting the initial allowance to the value that means infinite, `safeIncreaseAllowance()` can be used instead.

https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#IERC20-approve-address-uint256-

#### <ins>Proof Of Concept</ins>

```solidity
File: Vault.sol

212: CRV.approve(address(_amphClaimer), _takenCRV);
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L212

```solidity
File: Vault.sol

213: CVX.approve(address(_amphClaimer), _takenCVX);
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L213

```solidity
File: Vault.sol

321: IERC20(_token).approve(address(_booster), _amount);
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L321



#### <ins>Recommended Mitigation Steps</ins>

Consider using `safeIncreaseAllowance()` / `safeDecreaseAllowance()` instead.



### <a href="#qa-summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Condition will not revert when `block.timestamp` is `==` to the compared variable

The condition does not revert when block.timestamp is equal to the compared `>` or `<` variable. For example, if there is a check for `block.timestamp > expireTime` then there should be a check for cases where `block.timestamp` is `==` to `expireTime`
 
#### <ins>Proof Of Concept</ins>

```solidity
File: GovernorCharlie.sol

560: return (whitelistAccountExpirations[_account] > block.timestamp);
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L560

```solidity
File: ChainlinkOracleRelay.sol

59: if (block.timestamp > _updatedAt + stalePriceDelay) _stale = true;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L59







### <a href="#qa-summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Consider the case where totalsupply is 0

Consider the case where `totalsupply` is 0. When `totalsupply` is 0, it should return 0 directly, because there will be an error of dividing by 0.

#### <ins>Impact</ins>

This would cause the affected functions to revert and as a result can lead to potential loss.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: AMPHClaimer.sol

196: uint256 _bar = (_distributedAmph * 1e12) / (BASE_SUPPLY_PER_CLIFF * _FIFTY);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L196

```solidity
File: AMPHClaimer.sol

250: _cliff = _distributedAmph / BASE_SUPPLY_PER_CLIFF;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L250

```solidity
File: USDA.sol

262: _gonsPerFragment = _totalGons / _totalSupply;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L262

```solidity
File: USDA.sol

269: _e18reserveRatio = _safeu192((reserveAmount * EXP_SCALE) / _totalSupply);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L269

```solidity
File: WUSDA.sol

247: _wusdaAmount = (_usdaAmount * MAX_wUSDA_SUPPLY) / _totalUsdaSupply;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L247

```solidity
File: WUSDA.sol

255: _usdaAmount = (_wusdaAmount * _totalUsdaSupply) / MAX_wUSDA_SUPPLY;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L255



</details>


#### <ins>Recommended Mitigation Steps</ins>
Add check for zero value and return 0.

```solidity
if ( totalSupply() == 0) return 0;
```



### <a href="#qa-summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Constant decimal values

The use of fixed decimal values such as 1e18 or 1e8 in Solidity contracts can lead to inaccuracies, bugs, and vulnerabilities, particularly when interacting with tokens having different decimal configurations. Not all ERC20 tokens follow the standard 18 decimal places, and assumptions about decimal places can lead to miscalculations.

Resolution: Always retrieve and use the decimals() function from the token contract itself when performing calculations involving token amounts. This ensures that your contract correctly handles tokens with any number of decimal places, mitigating the risk of numerical errors or under/overflows that could jeopardize contract integrity and user funds.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: AMPHClaimer.sol

88: if (_crvAmountToSend != 0 && _claimedAmph != 0) distributedAmph += (_claimedAmph / 1e12);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L88

```solidity
File: AMPHClaimer.sol

177: if (_getCliff((_amphByCrv / 1e12) + distributedAmph) >= TOTAL_CLIFFS) return (0, 0, 0);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L177

```solidity
File: AMPHClaimer.sol

196: uint256 _bar = (_distributedAmph * 1e12) / (BASE_SUPPLY_PER_CLIFF * _FIFTY);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L196

```solidity
File: AMPHClaimer.sol

222: _amphForThisTurn = ((_rate * _tempAmountReceived) / 1e12) / 1e6;
232: uint256 _amountTokenToEnter = (_amphAvailableForThisCliff * 1e18) / _rate;
241: _amphToMint += (_amphForThisTurn * 1e12);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L222

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L232

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L241



```solidity
File: VaultController.sol

667: _vault.modifyLiability(false, (_usdaToRepurchase * 1e18) / interest.factor);
670: totalBaseLiability -= _safeu192((_usdaToRepurchase * 1e18) / interest.factor);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L667

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L670



```solidity
File: VaultController.sol

790: uint256 _maxTokensToLiquidate = (_usdaToSolvency * 1e18) / _denominator;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L790

```solidity
File: ChainlinkTokenOracleRelay.sol

51: _value = (_aggregatorPrice * _baseAggregatorPrice) / 1e18;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L51

```solidity
File: StableCurveLpOracle.sol

53: _value = _lpPrice / 1e18;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L53

```solidity
File: UniswapV3TokenOracleRelay.sol

37: _price = (_ethPrice * _priceInEth) / 1e18;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/UniswapV3TokenOracleRelay.sol#L37

```solidity
File: WstEthOracle.sol

32: _value = (_stETHPrice * _stETHPerWstEth) / 1e18;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/WstEthOracle.sol#L32



</details>







### <a href="#qa-summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Function can risk gas exhaustion on large receipt calls due to multiple mandatory loops

The function contains loops over arrays that the user cannot influence. 
In any call for the function, users spend gas to iterate over the array. There are loops in the following functions that iterate all array values. Which could risk gas exhaustion on large array calls.

This risk is small, but could be lessened somewhat by allowing separate calls for small loops.
Consider allowing partial calls via array arguments, or detecting expensive call bundles and only partially iterating over them. Since the logic already filters out calls, this would make the later loops smaller.

#### <ins>Proof Of Concept</ins>


```solidity
File: VaultController.sol

988: function vaultSummaries(
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

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L988









### <a href="#qa-summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> Minting tokens to the zero address should be avoided

The core function `mint` is used by users to mint an option position by providing token1 as collateral and borrowing the max amount of liquidity. `Address(0)` check is missing in both this function and the internal function `_mint`, which is triggered to mint the tokens to the to address. Consider applying a check in the function to ensure tokens aren't minted to the zero address.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: USDA.sol


function mint(uint256 _susdAmount) external override paysInterest onlyOwner {
    if (_susdAmount == 0) revert USDA_ZeroAmount();
    _mint(_msgSender(), _susdAmount);
  }

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L161

```solidity
File: USDA.sol


function vaultControllerMint(address _target, uint256 _amount) external override onlyVaultController whenNotPaused {
    _mint(_target, _amount);
  }

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L224

```solidity
File: Vault.sol


function minter() external view override returns (address _minter) {
    _minter = vaultInfo.minter;
  }

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L75

```solidity
File: WUSDA.sol


function mint(uint256 _wusdaAmount) external override returns (uint256 _usdaAmount) {
    _usdaAmount = _wUSDAToUSDA(_wusdaAmount, _usdaSupply());
    _deposit(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
  }

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L59

```solidity
File: WUSDA.sol


function mintFor(address _to, uint256 _wusdaAmount) external override returns (uint256 _usdaAmount) {
    _usdaAmount = _wUSDAToUSDA(_wusdaAmount, _usdaSupply());
    _deposit(_msgSender(), _to, _usdaAmount, _wusdaAmount);
  }

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L70



</details>





### <a href="#qa-summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> The Contract Should `approve(0)` First

Some tokens (like USDT L199) do not work when changing the allowance from an existing non-zero allowance value. They must first be approved by zero and then the actual allowance must be approved.

#### <ins>Proof Of Concept</ins>

```solidity
File: Vault.sol

321: IERC20(_token).approve(address(_booster), _amount);
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L321



#### <ins>Recommended Mitigation Steps</ins>

Approve with a zero amount first before setting the actual amount.



### <a href="#qa-summary">[LOW&#x2011;8]</a><a name="LOW&#x2011;8"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

<details>

```solidity
File: AMPHClaimer.sol

4: import {Math} from '@openzeppelin/contracts/utils/math/Math.sol';
6: import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';
7: import {SafeERC20, IERC20} from '@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L4-L7

```solidity
File: USDA.sol

11: import {IERC20Metadata, IERC20} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';
12: import {Pausable} from '@openzeppelin/contracts/security/Pausable.sol';
13: import {Context} from '@openzeppelin/contracts/utils/Context.sol';
14: import {EnumerableSet} from '@openzeppelin/contracts/utils/structs/EnumerableSet.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L11-L14

```solidity
File: Vault.sol

12: import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';
13: import {Context} from '@openzeppelin/contracts/utils/Context.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L12-L13

```solidity
File: VaultController.sol

16: import {IERC20, IERC20Metadata} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';
17: import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';
18: import {Pausable} from '@openzeppelin/contracts/security/Pausable.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L16-L18

```solidity
File: VaultDeployer.sol

7: import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultDeployer.sol#L7

```solidity
File: WUSDA.sol

6: import {SafeERC20} from '@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol';
7: import {ERC20, IERC20} from '@openzeppelin/contracts/token/ERC20/ERC20.sol';
9: import {ERC20Permit} from '@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L6-L9

```solidity
File: AmphoraProtocolToken.sol

5: import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';
6: import {ERC20, ERC20Permit, ERC20VotesComp} from '@openzeppelin/contracts/token/ERC20/extensions/ERC20VotesComp.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L5-L6

```solidity
File: CurveMaster.sol

7: import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/CurveMaster.sol#L7

```solidity
File: CbEthEthOracle.sol

6: import {Math} from '@openzeppelin/contracts/utils/math/Math.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol#L6

```solidity
File: ChainlinkOracleRelay.sol

7: import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L7

```solidity
File: CTokenOracle.sol

6: import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';
7: import {IERC20Metadata} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L6-L7

```solidity
File: StableCurveLpOracle.sol

6: import {Math} from '@openzeppelin/contracts/utils/math/Math.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L6

```solidity
File: UniswapV3OracleRelay.sol

7: import {IERC20Metadata} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L7

```solidity
File: UFragments.sol

5: import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';
6: import {IERC20Metadata} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L5-L6

```solidity
File: IAMPHClaimer.sol

4: import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IAMPHClaimer.sol#L4

```solidity
File: IUSDA.sol

6: import {IERC20Metadata, IERC20} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IUSDA.sol#L6

```solidity
File: IVault.sol

5: import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IVault.sol#L5

```solidity
File: IVaultDeployer.sol

6: import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IVaultDeployer.sol#L6

```solidity
File: IWUSDA.sol

4: import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IWUSDA.sol#L4

```solidity
File: IBaseRewardPool.sol

4: import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/IBaseRewardPool.sol#L4

```solidity
File: ICurvePool.sol

4: import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L4

```solidity
File: ICVX.sol

4: import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICVX.sol#L4

```solidity
File: IRoles.sol

4: import {IAccessControl} from '@openzeppelin/contracts/access/IAccessControl.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/IRoles.sol#L4

```solidity
File: IVirtualBalanceRewardPool.sol

4: import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/IVirtualBalanceRewardPool.sol#L4



</details>

#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.
See https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/tree/master/contracts for list of available upgradeable contracts





### <a href="#qa-summary">[LOW&#x2011;9]</a><a name="LOW&#x2011;9"> No storage gap for upgradeable contract might lead to storage slot collision

For upgradeable contracts, there must be storage gap to "allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments". Otherwise it may be very difficult to write new implementation code. Without storage gap, the variable in child contract might be overwritten by the upgraded base contract if new variables are added to the base contract. This could have unintended and very serious consequences to the child contracts.

Refer to the bottom part of this article: https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable

#### <ins>Proof Of Concept</ins>

However, the contract doesn't contain a storage gap. The storage gap is essential for upgradeable contract because "It allows us to freely add new state variables in the future without compromising the storage compatibility with existing deployments". See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```solidity
File: Vault.sol
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L14



#### <ins>Recommended Mitigation Steps</ins>
Recommend adding appropriate storage gap at the end of upgradeable contracts such as the below. Please reference OpenZeppelin upgradeable contract templates.
```solidity
    uint256[50] private __gap;
```






### <a href="#qa-summary">[LOW&#x2011;10]</a><a name="LOW&#x2011;10"> Use SafeCast to safely downcast variables

Downcasting `int`/`uint`s in Solidity can be unsafe due to the potential for data loss and unintended behavior. When downcasting a larger integer type to a smaller one (e.g., `uint256` to `uint128`), the value may exceed the range of the target type, leading to truncation and loss of significant digits. This data loss can result in unexpected state changes, incorrect calculations, or other contract vulnerabilities, ultimately compromising the contracts functionality and reliability. To prevent these risks, developers should carefully consider the range of values their variables may hold and ensure that proper checks are in place to prevent out-of-range values before performing downcasting. Also consider using OZ SafeCast functionality.

#### <ins>Proof Of Concept</ins>

```solidity
File: VaultController.sol

637: _collateralLiquidated -= getLiquidationFee(uint192(_collateralLiquidated), _assetAddress);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L637

```solidity
File: VaultController.sol

681: uint192 _liquidationFee = getLiquidationFee(uint192(_tokensToLiquidate), _assetAddress);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L681

```solidity
File: VaultController.sol

930: uint64 _timeDifference = uint64(block.timestamp) - interest.lastTime;
957: interest = Interest(uint64(block.timestamp), interest.factor + _e18FactorIncrease);
970: emit InterestEvent(uint64(block.timestamp), _e18FactorIncrease, _curveVal);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L930

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L957

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L970





## Non Critical Issues

### <a href="#qa-summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Consider adding a deny-list

Doing so will significantly increase centralization, but will help to prevent hackers from using stolen tokens

#### <ins>Proof Of Concept</ins>


```solidity
File: WUSDA.sol

28: contract WUSDA is IWUSDA, ERC20, ERC20Permit

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L28

```solidity
File: AmphoraProtocolToken.sol

8: contract AmphoraProtocolToken is IAmphoraProtocolToken, ERC20VotesComp, Ownable

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L8

```solidity
File: UFragments.sol

20: contract UFragments is Ownable, IERC20Metadata

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L20







### <a href="#qa-summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Consistent usage of require vs custom error

Consider using the same approach throughout the codebase to improve the consistency of the code.

#### <ins>Proof Of Concept</ins>


```solidity
File: AnchoredViewRelay.sol

51: if (_underlying != mainRelay.underlying()) revert OracleRelay_DifferentUnderlyings();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L51

```solidity
File: UFragments.sol

57: if (_to == address(0) || _to == address(this)) revert UFragments_InvalidRecipient();
331: revert UFragments_InvalidSignature();
334: revert UFragments_InvalidSignature();

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L57

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L331

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L334








### <a href="#qa-summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Constant redefined elsewhere

Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A <a href="https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678">cheap way</a> to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

#### <ins>Proof Of Concept</ins>

```solidity
File: UFragments.sol

78: uint8 public constant decimals = uint8(DECIMALS);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L78






### <a href="#qa-summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> block.timestamp is already used when emitting events, no need to input timestamp

#### <ins>Proof Of Concept</ins>

```solidity
File: VaultController.sol

970: emit InterestEvent(uint64(block.timestamp), _e18FactorIncrease, _curveVal);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L970








### <a href="#qa-summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Mint events missing key information

Some events are missing key information when emitted.
In the following events, they should contain the minted amount and beneficiary to whom it was minted.

#### <ins>Proof Of Concept</ins>


```solidity
File: USDA.sol

177: emit Mint(_target, _amount);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L177







### <a href="#qa-summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Empty `bytes` check is missing

To avoid mistakenly accepting empty `bytes` as a valid value, consider to check for empty `bytes`. This helps ensure that empty `bytes` are not treated as valid inputs, preventing potential issues.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: GovernorCharlie.sol

290: function _queueTransaction(
    address _target,
    uint256 _value,
    string memory _signature,
    bytes memory _data,
    uint256 _eta,
    uint256 _delay
  ) internal returns (bytes32 _txHash) {
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L290

```solidity
File: GovernorCharlie.sol

329: function executeTransaction(
    address _target,
    uint256 _value,
    string memory _signature,
    bytes memory _data,
    uint256 _eta
  ) external payable override {
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L329

```solidity
File: GovernorCharlie.sol

399: function _cancelTransaction(
    address _target,
    uint256 _value,
    string memory _signature,
    bytes memory _data,
    uint256 _eta
  ) internal {
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L399

```solidity
File: GovernorCharlie.sol

507: function castVoteBySig(uint256 _proposalId, uint8 _support, uint8 _v, bytes32 _r, bytes32 _s) external override {
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L507

```solidity
File: GovernorCharlie.sol

507: function castVoteBySig(uint256 _proposalId, uint8 _support, uint8 _v, bytes32 _r, bytes32 _s) external override {
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L507

```solidity
File: UFragments.sol

316: function permit(
    address _owner,
    address _spender,
    uint256 _value,
    uint256 _deadline,
    uint8 _v,
    bytes32 _r,
    bytes32 _s
  ) public {
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L316

```solidity
File: UFragments.sol

316: function permit(
    address _owner,
    address _spender,
    uint256 _value,
    uint256 _deadline,
    uint8 _v,
    bytes32 _r,
    bytes32 _s
  ) public {
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L316



</details>




### <a href="#qa-summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Function names should differ to make the code more readable

In Solidity, while function overriding allows for functions with the same name to coexist, it is advisable to avoid this practice to enhance code readability and maintainability. Having multiple functions with the same name, even with different parameters or in inherited contracts, can cause confusion and increase the likelihood of errors during development, testing, and debugging. Using distinct and descriptive function names not only clarifies the purpose and behavior of each function, but also helps prevent unintended function calls or incorrect overriding. By adopting a clear and consistent naming convention, developers can create more comprehensible and maintainable smart contracts.

#### <ins>Proof Of Concept</ins>


```solidity
File: ICurvePool.sol

16: function remove_liquidity
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L16

```solidity
File: ICurvePool.sol

34: function remove_liquidity
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L34

```solidity
File: ICurvePool.sol

20: function add_liquidity
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L20

```solidity
File: ICurvePool.sol

29: function add_liquidity
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L29






### <a href="#qa-summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Function must not be longer than 50 lines

#### <ins>Proof Of Concept</ins>


```solidity
File: Vault.sol

164: function claimRewards(

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L164

```solidity
File: GovernorCharlie.sol

159: function _propose(

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L159






### <a href="#qa-summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> Functions that alter state should emit events

#### <ins>Proof Of Concept</ins>


```solidity
File: GovernorCharlie.sol

567: function setNewToken(address _token) external onlyGov {
    amph = IAMPH(_token);
  }
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L567

```solidity
File: GovernorCharlie.sol

575: function setMaxWhitelistPeriod(uint256 _second) external onlyGov {
    maxWhitelistPeriod = _second;
  }
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L575

```solidity
File: ChainlinkOracleRelay.sol

65: function setStalePriceDelay(uint256 _stalePriceDelay) external onlyOwner {
    if (_stalePriceDelay == 0) revert ChainlinkOracle_ZeroAmount();
    stalePriceDelay = _stalePriceDelay;
  }
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L65

```solidity
File: CTokenOracle.sol

57: function changeAnchoredView(address _anchoredViewUnderlying) external onlyOwner {
    anchoredViewUnderlying = IOracleRelay(_anchoredViewUnderlying);
  }
}
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L57






### <a href="#qa-summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Generate perfect code headers for better solidity code layout and readability

It is recommended to use pre-made headers for Solidity code layout and readability:
https://github.com/transmissions11/headers

```solidity
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```


#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: AMPHClaimer.sol

5: AMPHClaimer.sol

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L5

```solidity
File: Vault.sol

5: Vault.sol

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L5

```solidity
File: VaultController.sol

9: VaultController.sol

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L9

```solidity
File: VaultDeployer.sol

5: VaultDeployer.sol

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultDeployer.sol#L5

```solidity
File: WUSDA.sol

4: WUSDA.sol

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L4

```solidity
File: AmphoraProtocolToken.sol

4: AmphoraProtocolToken.sol

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L4

```solidity
File: GovernorCharlie.sol

7: GovernorCharlie.sol

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L7

```solidity
File: CurveMaster.sol

4: CurveMaster.sol

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/CurveMaster.sol#L4

```solidity
File: OracleRelay.sol

4: OracleRelay.sol

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L4



</details>



### <a href="#qa-summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> `internal` functions not called by the contract should be removed

#### <ins>Proof Of Concept</ins>

```solidity
File: EthSafeStableCurveOracle.sol

function _getVirtualPrice() internal view override returns (uint256 _value) {
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol#L48




### <a href="#qa-summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> Numeric values having to do with time should use time units for readability

There are <a href='https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units'>units</a> for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

#### <ins>Proof Of Concept</ins>

```solidity
File: GovernorCharlie.sol

101: optimisticVotingDelay = 25_600;

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L101





### <a href="#qa-summary">[NC&#x2011;49]</a><a name="NC&#x2011;49"> Use a more recent version of Solidity

<a href="https://blog.soliditylang.org/2022/02/16/solidity-0.8.12-release-announcement/">0.8.12</a>: 
string.concat() instead of abi.encodePacked(<str>,<str>)

<a href="https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/">0.8.13</a>: 
Ability to use using for with a list of free functions

<a href="https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/">0.8.14</a>:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

<a href="https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/">0.8.15</a>:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

<a href="https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/">0.8.16</a>:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

<a href="https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/">0.8.17</a>:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

<a href="https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/">0.8.19</a>:
SMTChecker: New trusted mode that assumes that any compile-time available code is the actual used code, even in external calls. 
Bug Fixes: 
- Assembler: Avoid duplicating subassembly bytecode where possible.
- Code Generator: Avoid including references to the deployed label of referenced functions if they are called right away.
- ContractLevelChecker: Properly distinguish the case of missing base constructor arguments from having an unimplemented base function.
- SMTChecker: Fix internal error caused by unhandled z3 expressions that come from the solver when bitwise operators are used.
- SMTChecker: Fix internal error when using the custom NatSpec annotation to abstract free functions.
- TypeChecker: Also allow external library functions in using for.

<a href="https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/">0.8.20</a>:
- Assembler: Use push0 for placing 0 on the stack for EVM versions starting from “Shanghai”. This decreases the deployment and runtime costs.
- EVM: Set default EVM version to “Shanghai”.
- EVM: Support for the EVM Version “Shanghai”.
- Optimizer: Re-implement simplified version of UnusedAssignEliminator and UnusedStoreEliminator. It can correctly remove some unused assignments in deeply nested loops that were ignored by the old version.
- Parser: Unary plus is no longer recognized as a unary operator in the AST and triggers an error at the parsing stage (rather than later during the analysis).
- SMTChecker: Group all messages about unsupported language features in a single warning. The CLI option --model-checker-show-unsupported and the JSON option settings.modelChecker.showUnsupported can be enabled to show the full list.
- SMTChecker: Properties that are proved safe are now reported explicitly at the end of analysis. By default, only the number of safe properties is shown. The CLI option --model-checker-show-proved-safe and the JSON option settings.modelChecker.showProvedSafe can be enabled to show the full list of safe properties.
- Standard JSON Interface: Add experimental support for importing ASTs via Standard JSON.
- Yul EVM Code Transform: If available, use push0 instead of codesize to produce an arbitrary value on stack in order to create equal stack heights between branches.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: AMPHClaimer.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/AMPHClaimer.sol#L2

```solidity
File: USDA.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L2

```solidity
File: Vault.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/Vault.sol#L2

```solidity
File: VaultController.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L2

```solidity
File: VaultDeployer.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultDeployer.sol#L2

```solidity
File: WUSDA.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/WUSDA.sol#L2

```solidity
File: AmphoraProtocolToken.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L2

```solidity
File: GovernorCharlie.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L3

```solidity
File: CurveMaster.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/CurveMaster.sol#L2

```solidity
File: AnchoredViewRelay.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol#L2

```solidity
File: CbEthEthOracle.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol#L2

```solidity
File: ChainlinkOracleRelay.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L2

```solidity
File: ChainlinkStalePriceLib.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol#L2

```solidity
File: ChainlinkTokenOracleRelay.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L2

```solidity
File: CTokenOracle.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L2

```solidity
File: CurveRegistryUtils.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol#L2

```solidity
File: EthSafeStableCurveOracle.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol#L2

```solidity
File: OracleRelay.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L2

```solidity
File: StableCurveLpOracle.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol#L2

```solidity
File: TriCrypto2Oracle.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/TriCrypto2Oracle.sol#L2

```solidity
File: UniswapV3OracleRelay.sol

pragma solidity >=0.5.0 <0.9.0;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L2

```solidity
File: UniswapV3TokenOracleRelay.sol

pragma solidity >=0.5.0 <0.9.0;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/UniswapV3TokenOracleRelay.sol#L2

```solidity
File: WstEthOracle.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/WstEthOracle.sol#L2

```solidity
File: GovernanceStructs.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/GovernanceStructs.sol#L2

```solidity
File: ThreeLines0_100.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/ThreeLines0_100.sol#L2

```solidity
File: UFragments.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L3

```solidity
File: IAMPHClaimer.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IAMPHClaimer.sol#L2

```solidity
File: IUSDA.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IUSDA.sol#L2

```solidity
File: IVault.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IVault.sol#L2

```solidity
File: IVaultController.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IVaultController.sol#L2

```solidity
File: IVaultDeployer.sol

pragma solidity >=0.8.4 <0.9.0;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IVaultDeployer.sol#L2

```solidity
File: IWUSDA.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IWUSDA.sol#L2

```solidity
File: IAMPH.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/governance/IAMPH.sol#L2

```solidity
File: IAmphoraProtocolToken.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/governance/IAmphoraProtocolToken.sol#L2

```solidity
File: IGovernorCharlie.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/governance/IGovernorCharlie.sol#L2

```solidity
File: IChainlinkOracleRelay.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/periphery/IChainlinkOracleRelay.sol#L2

```solidity
File: ICToken.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/periphery/ICToken.sol#L2

```solidity
File: ICurveMaster.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/periphery/ICurveMaster.sol#L2

```solidity
File: IOracleRelay.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/periphery/IOracleRelay.sol#L2

```solidity
File: IBaseRewardPool.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/IBaseRewardPool.sol#L2

```solidity
File: IBooster.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/IBooster.sol#L2

```solidity
File: ICurvePool.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L2

```solidity
File: ICurveSlave.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurveSlave.sol#L2

```solidity
File: ICVX.sol

pragma solidity >=0.8.4 <0.9.0;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICVX.sol#L2

```solidity
File: IRoles.sol

pragma solidity >=0.8.4 <0.9.0;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/IRoles.sol#L2

```solidity
File: IVirtualBalanceRewardPool.sol

pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/IVirtualBalanceRewardPool.sol#L2



</details>

#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.



### <a href="#qa-summary">[NC&#x2011;50]</a><a name="NC&#x2011;50"> Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters
The following events should also add the previous original value in addition to the new value.

#### <ins>Proof Of Concept</ins>


```solidity
File: UFragments.sol

40: event LogMonetaryPolicyUpdated(address monetaryPolicy);
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L40

```solidity
File: IVaultController.sol

29: event NewProtocolFee(uint192 _protocolFee);
```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/core/IVaultController.sol#L29




#### <ins>Recommended Mitigation Steps</ins>
The events should include the new value and old value where possible.



### <a href="#qa-summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Overly complicated arithmetic

To maintain readability in code, particularly in Solidity which can involve complex mathematical operations, it is often recommended to limit the number of arithmetic operations to a maximum of 2-3 per line. Too many operations in a single line can make the code difficult to read and understand, increase the likelihood of mistakes, and complicate the process of debugging and reviewing the code. Consider splitting such operations over more than one line, take special care when dealing with division however. Try to limit the number of arithmetic operations to a maximum of 3 per line.

#### <ins>Proof Of Concept</ins>


```solidity
File: VaultController.sol

948: _truncate((uint256(_timeDifference) * uint256(1e18) * uint256(_curveVal)) / (365 days + 6 hours))

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L948

```solidity
File: UniswapV3OracleRelay.sol

85: _e18Amount = (_decimals > 18) ? _amount / (10 ** (_decimals - 18)) : _amount * (10 ** (18 - _decimals));

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L85




### <a href="#qa-summary">[NC&#x2011;14]</a><a name="NC&#x2011;14"> It is standard for all external and public functions to be overridden from an interface

Check that all public or external functions are override. This is iseful to make sure that the whole API is extracted in an interface.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: USDA.sol

212: function recoverDust(address _to) external onlyOwner {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L212

```solidity
File: USDA.sol

278: function addVaultController(address _vaultController) external onlyOwner {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L278

```solidity
File: USDA.sol

287: function removeVaultController(address _vaultController) external onlyOwner {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L287

```solidity
File: USDA.sol

297: function removeVaultControllerFromList(address _vaultController) external onlyOwner {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/USDA.sol#L297

```solidity
File: VaultController.sol

462: function checkVault(uint96 _id) public returns (bool _overCollateralized) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L462

```solidity
File: VaultDeployer.sol

27: function deployVault(uint96 _id, address _minter) external returns (IVault _vault) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultDeployer.sol#L27

```solidity
File: AmphoraProtocolToken.sol

20: function mint(address _dst, uint256 _rawAmount) public onlyOwner {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L20

```solidity
File: GovernorCharlie.sol

439: function getProposal(uint256 _proposalId) external view returns (Proposal memory _proposal) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L439

```solidity
File: GovernorCharlie.sol

567: function setNewToken(address _token) external onlyGov {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L567

```solidity
File: GovernorCharlie.sol

575: function setMaxWhitelistPeriod(uint256 _second) external onlyGov {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L575

```solidity
File: ChainlinkOracleRelay.sol

57: function isStale() external view returns (bool _stale) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L57

```solidity
File: ChainlinkOracleRelay.sol

65: function setStalePriceDelay(uint256 _stalePriceDelay) external onlyOwner {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L65

```solidity
File: ChainlinkTokenOracleRelay.sol

40: function isStale() external view returns (bool _stale) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/ChainlinkTokenOracleRelay.sol#L40

```solidity
File: CTokenOracle.sol

57: function changeAnchoredView(address _anchoredViewUnderlying) external onlyOwner {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L57

```solidity
File: OracleRelay.sol

24: function currentValue() external virtual returns (uint256 _currentValue) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L24

```solidity
File: UFragments.sol

111: function setMonetaryPolicy(address _monetaryPolicy) external onlyOwner {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L111

```solidity
File: UFragments.sol

136: function scaledBalanceOf(address _who) external view returns (uint256 _balance) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L136

```solidity
File: UFragments.sol

144: function scaledTotalSupply() external view returns (uint256 __totalGons) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L144

```solidity
File: UFragments.sol

153: function nonces(address _who) public view returns (uint256 _addressNonces) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L153

```solidity
File: UFragments.sol

163: function DOMAIN_SEPARATOR() public view returns (bytes32 _domainSeparator) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L163

```solidity
File: UFragments.sol

194: function transferAll(address _to) external validRecipient(_to) returns (bool _success) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L194

```solidity
File: UFragments.sol

243: function transferAllFrom(address _from, address _to) external validRecipient(_to) returns (bool _success) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L243

```solidity
File: UFragments.sol

283: function increaseAllowance(address _spender, uint256 _addedValue) public returns (bool _success) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L283

```solidity
File: UFragments.sol

297: function decreaseAllowance(address _spender, uint256 _subtractedValue) external returns (bool _success) {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L297

```solidity
File: UFragments.sol

315: function permit(
    address _owner,
    address _spender,
    uint256 _value,
    uint256 _deadline,
    uint8 _v,
    bytes32 _r,
    bytes32 _s
  ) public {

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/utils/UFragments.sol#L315

```solidity
File: ICurvePool.sol

7: function get_virtual_price() external view returns (uint256 _price);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L7

```solidity
File: ICurvePool.sol

15: function lp_token() external view returns (IERC20 _lpToken);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/interfaces/utils/ICurvePool.sol#L15



</details>



### <a href="#qa-summary">[NC&#x2011;16]</a><a name="NC&#x2011;16"> Use `""` instead of `"0x00"` for empty bytes to avoid triggering extra computation on transfers

When tokens with hooks on transfer take in `bytes` arguments, there is often a length check so that 0 length bytes inputs are skipped. By using `"0x00"`, a non-0 `bytes` value is created which is semantically empty but would fail the 0-length check. This would increase the gas cost of processing such transfers.



#### <ins>Proof Of Concept</ins>


```solidity
File: VaultController.sol

940: int256 _intCurveVal = curveMaster.getValueAt(address(0x00), _reserveRatio);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/core/VaultController.sol#L940



#### <ins>Recommended Mitigation Steps</ins>

Consider changing the value to `""` instead, unless there is some other reason to retain the bytes value as-is.


### <a href="#qa-summary">[NC&#x2011;17]</a><a name="NC&#x2011;17"> Use named function calls

Code base has an extensive use of named function calls, but it somehow missed one instance where this would be appropriate.

It should use named function calls on function call, as such:
```solidity
    library.exampleFunction{value: _data.amount.value}({
        _id: _data.id,
        _amount: _data.amount.value,
        _token: _data.token,
        _example: "",
        _metadata: _data.metadata
    });
```

#### <ins>Proof Of Concept</ins>


```solidity
File: GovernorCharlie.sol

314: this.executeTransaction{value: _proposal.values[_i]}(
        _proposal.targets[_i], _proposal.values[_i], _proposal.signatures[_i], _proposal.calldatas[_i], _proposal.eta
      );

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L314

```solidity
File: GovernorCharlie.sol

350: (bool _success, ) = _target.call{value: _value}(_callData);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L350







### <a href="#qa-summary">[NC&#x2011;18]</a><a name="NC&#x2011;18"> Use SMTChecker

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19




### <a href="#qa-summary">[NC&#x2011;19]</a><a name="NC&#x2011;19"> Cast to `bytes` or `bytes32` for clearer semantic meaning

Using a <a href="https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739">cast</a> on a single argument, rather than abi.encodePacked() makes the intended operation more clear, leading to less reviewer confusion.

#### <ins>Proof Of Concept</ins>

```solidity
File: GovernorCharlie.sol

347: else _callData = abi.encodePacked(bytes4(keccak256(bytes(_signature))), _data);

```

https://github.com/code-423n4/2023-07-amphora/tree/main/core/solidity/contracts/governance/GovernorCharlie.sol#L347





