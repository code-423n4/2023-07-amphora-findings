# LOW

# Summary
|      |   issue    |  instance  |
|------|------------|------------|
|[G-01]| _safeMint() should be used rather than _mint() wherever possible|6|
|[G-02]| Contracts are not using their OZ upgradeable counterparts|35|
|[L-03]| no check if the burn amount is zero or not|2|
|[L-04]| Use fixed compiler version|~|
|[L-05]| Ownable: Does not implement 2-Step-Process for transferring ownership|1|
|[L-06]| Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from various type int/uint values|24|
|[L-07]| Division before multiple can lead to precision errors|1|
|[L-08]| In the constructor, there is no return of incorrect address identification|1|
|[G-09]| Signature use at deadlines should be allowed|2|
|[L-10]| Some ERC20 tokens should need to approve(0) first|1|



## [L-01] _safeMint() should be used rather than _mint() wherever possible
_mint() is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

```solidity
File:    core/solidity/contracts/core/USDA.sol
104       _mint(_target, _susdAmount);

163       _mint(_msgSender(), _susdAmount);

225       _mint(_target, _amount);       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L104

```solidity
File:   core/solidity/contracts/core/WUSDA.sol
216   _mint(_to, _wusdaAmount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L216

```solidity
File:  core/solidity/contracts/governance/AmphoraProtocolToken.sol
16  _mint(_account, _initialSupply);

21  _mint(_dst, _rawAmount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L16

## [L-02] Contracts are not using their OZ upgradeable counterparts
Description
The non-upgradeable standard version of OpenZeppelin’s library, such as `Ownable`, `Pausable`, `Address`, `Context`, `SafeERC20`, `ERC1967Upgrade` etc, are inherited / used by both the proxy and the implementation contracts.

As a result, when attempting to use the upgrades plugin mentioned, the following errors are raised:

```
Error: Contract `FiatTokenV1` is not upgrade safe

contracts/v1/FiatTokenV1.sol:58: Variable `totalSupply_` is assigned an initial value
  Move the assignment to the initializer
  https://zpl.in/upgrades/error-004

contracts/v1/Pausable.sol:49: Variable `paused` is assigned an initial value
  Move the assignment to the initializer
  https://zpl.in/upgrades/error-004

contracts/v1/Ownable.sol:28: Contract `Ownable` has a constructor
  Define an initializer instead
  https://zpl.in/upgrades/error-001

contracts/util/Address.sol:186: Use of delegatecall is not allowed
  https://zpl.in/upgrades/error-002
       
```
Having reviewed these errors, none had any adversarial impact:

`totalSupply_` and `paused` are explictly assigned the default values `0` and `false`
the implementation contracts utilises the internal `_transferOwnership()` in the initializer, thus transferring ownership to `newOwner` regardless of who the current owner is
`Address’s` `delegatecall` is only used by the `ERC1967Upgrade` contract. Comparing both the `Address` and `ERC1967Upgrade` contracts against their upgradeable counterparts show similar behaviour (differences are some refactoring done to shift the delegatecall into the `ERC1967Upgrade` contract).
Nevertheless, it would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.



```solidity
File:  core/solidity/contracts/core/AMPHClaimer.sol
6   import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';

7   import {SafeERC20, IERC20} from '@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol';
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/AMPHClaimer.sol#L6

```solidity
File:  core/solidity/contracts/core/USDA.sol
import {IERC20Metadata, IERC20} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';
import {Pausable} from '@openzeppelin/contracts/security/Pausable.sol';
import {Context} from '@openzeppelin/contracts/utils/Context.sol';
import {EnumerableSet} from '@openzeppelin/contracts/utils/structs/EnumerableSet.sol';  
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L11-L14

```solidity
File:  core/solidity/contracts/core/Vault.sol
import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';
import {Context} from '@openzeppelin/contracts/utils/Context.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L12-L13

```solidity
File:  core/solidity/contracts/core/VaultController.sol
import {IERC20, IERC20Metadata} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';
import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';
import {Pausable} from '@openzeppelin/contracts/security/Pausable.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L16-L18

```solidity
File:  core/solidity/contracts/core/VaultDeployer.sol
7  import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultDeployer.sol#L7

```solidity
File:  core/solidity/contracts/core/WUSDA.sol
import {SafeERC20} from '@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol';
import {ERC20, IERC20} from '@openzeppelin/contracts/token/ERC20/ERC20.sol';
// solhint-disable-next-line max-line-length
import {ERC20Permit} from '@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L6-L9

```solidity
File:  core/solidity/contracts/governance/AmphoraProtocolToken.sol
import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';
import {ERC20, ERC20Permit, ERC20VotesComp} from '@openzeppelin/contracts/token/ERC20/extensions/ERC20VotesComp.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L5-L6

```solidity
File:  core/solidity/contracts/periphery/CurveMaster.sol
7   import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/CurveMaster.sol#L7


```solidity
File:  core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol
7   import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L7

```solidity
File:  core/solidity/contracts/periphery/oracles/CTokenOracle.sol
import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';
import {IERC20Metadata} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L6-L7

```solidity
File:  core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol
7  import {IERC20Metadata} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';
       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L7

```solidity
File:  core/solidity/contracts/utils/UFragments.sol
import {Ownable} from '@openzeppelin/contracts/access/Ownable.sol';
import {IERC20Metadata} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L5-L6

```solidity
File:  core/solidity/interfaces/core/IAMPHClaimer.sol
4   import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IAMPHClaimer.sol#L4

```solidity
File:  core/solidity/interfaces/core/IUSDA.sol
6   import {IERC20Metadata, IERC20} from '@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IUSDA.sol#L6

```solidity
File:  core/solidity/interfaces/core/IVault.sol
5   import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';     
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IVault.sol#L5

```solidity
File:  core/solidity/interfaces/core/IVaultDeployer.sol
6  import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';    
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IVaultDeployer.sol#L6

```solidity
File:  core/solidity/interfaces/core/IWUSDA.sol
4   import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';  
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/core/IWUSDA.sol#L4

```solidity
File:  core/solidity/interfaces/utils/IBaseRewardPool.sol
4  import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol'; 
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/utils/IBaseRewardPool.sol#L4

```solidity
File:  core/solidity/interfaces/utils/ICurvePool.sol
4   import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/utils/ICurvePool.sol#L4

```solidity
File:  core/solidity/interfaces/utils/ICVX.sol
4   import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';   
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/utils/ICVX.sol#L4

```solidity
File:  core/solidity/interfaces/utils/IRoles.sol
4  import {IAccessControl} from '@openzeppelin/contracts/access/IAccessControl.sol';    
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/utils/IRoles.sol#L4

```solidity
File:  core/solidity/interfaces/utils/IVirtualBalanceRewardPool.sol
4  import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';     
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/utils/IVirtualBalanceRewardPool.sol#L4

```solidity
File:  core/solidity/interfaces/utils/IWStETH.sol
4   import {IERC20} from '@openzeppelin/contracts/token/ERC20/IERC20.sol';  
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/interfaces/utils/IWStETH.sol#L4

### Recommended Mitigation Steps
Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.

## [L-03] no check if the burn amount is zero or not
if the amount is zero so the unhecked block will divided zero on 3 and we use gas for nothing ! if we set zero we may pass the _burn checks, i know it is passed by only permit but it's better to avoid this happen because it's seting by human and it means it can be set with 0 balance to burn !

```solidity
File:  core/solidity/contracts/core/WUSDA.sol
227       _burn(_from, _wusdaAmount);    
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L227

```solidity
File:  core/solidity/contracts/core/USDA.sol
233       _burn(_target, _amount);       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/USDA.sol#L233


## [L-04] Use fixed compiler version
All in scope contracts use`^0.8.0` as compiler version.

They should use a fixed version, i.e. `0.8.12`, to make sure the contracts are always compiled with the intended version.


## [L-05] Ownable: Does not implement 2-Step-Process for transferring ownership
UFragments inherits from the Ownable contract.
This contract does not implement a 2-Step-Process for transferring ownership.
So ownership of the contract can easily be lost when making a mistake when transferring ownership.

Consider using the Ownable2Step contract from OZ
(https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol) instead.

```solidity
File:  core/solidity/contracts/utils/UFragments.sol
20   contract UFragments is Ownable, IERC20Metadata {  
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L20


## [L-06] Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from various type int/uint values

```solidity
File:  core/solidity/contracts/utils/UFragments.sol
78    uint8 public constant decimals = uint8(DECIMALS);  

330      if (uint256(_s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) {
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/utils/UFragments.sol#L78

```solidity
File:   core/solidity/contracts/governance/GovernorCharlie.sol
514       if (uint256(_s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) {       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L514

```solidity
File:  core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol
75       _value = (uint256(_latest) * MULTIPLY) / DIVIDE;  
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L75

```solidity
File:  core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol
14       _price = uint256(_answer);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol#L14

```solidity
File:  core/solidity/contracts/core/VaultController.sol
103        interest = Interest(uint64(block.timestamp), 1 ether);

501       _fee = _safeu192(_truncate(uint256(_amount * (1e18 + initialBorrowingFee)))) - _amount;

515        _safeu192(_truncate(uint256(_tokensToLiquidate * (1e18 + _liquidationIncentive)))) - _tokensToLiquidate;

518        _safeu192(_truncate(uint256(_liquidatorExpectedProfit * (1e18 + liquidationFee)))) - _liquidatorExpectedProfit;

535      uint192 _baseAmount = _safeu192(uint256((_amount + _fee) * EXP_SCALE) / uint256(interest.factor));

543       uint256 _usdaLiability = _truncate(uint256(interest.factor) * _baseLiability);

637      _collateralLiquidated -= getLiquidationFee(uint192(_collateralLiquidated), _assetAddress);

681       uint192 _liquidationFee = getLiquidationFee(uint192(_tokensToLiquidate), _assetAddress);

930      uint64 _timeDifference = uint64(block.timestamp) - interest.lastTime;

935      uint256 _ui18 = uint256(usda.reserveRatio());

937      int256 _reserveRatio = int256(_ui18);

942       uint192 _curveVal = _safeu192(uint256(_intCurveVal));

948  _  truncate((uint256(_timeDifference) * uint256(1e18) * uint256(_curveVal)) / (365 days + 6 hours))
          * uint256(interest.factor)

953      uint192 _valueBefore = _safeu192(_truncate(uint256(totalBaseLiability) * uint256(interest.factor)));


957      interest = Interest(uint64(block.timestamp), interest.factor + _e18FactorIncrease);

959      uint192 _valueAfter = _safeu192(_truncate(uint256(totalBaseLiability) * uint256(interest.factor)));

963       uint192 _protocolAmount = _safeu192(_truncate(uint256(_valueAfter - _valueBefore) * uint256(protocolFee)));


970    emit InterestEvent(uint64(block.timestamp), _e18FactorIncrease, _curveVal);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/VaultController.sol#L103




## [L-07] Division before multiple can lead to precision errors

```solidity
File:  core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol
85       _e18Amount = (_decimals > 18) ? _amount / (10 ** (_decimals - 18)) : _amount * (10 ** (18 - _decimals));       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L85

### Recommended Mitigation Steps
Multiply first before dividing to keep the precision.
code like this
```solidity
 function _toBase18(uint256 _amount, uint8 _decimals) internal pure returns (uint256 _e18Amount) {
    _e18Amount = (_decimals > 18) ? _amount * (10 ** (18 - _decimals)) / (10 ** (_decimals - 18)) : _amount * (10 ** (18 - _decimals));
} 
```

## [L-08] In the constructor, there is no return of incorrect address identification
In case of incorrect address definition in the constructor , there is no way to fix it because of the variables are immutable.

It is recommended to fix the architecture:

Address definitions can be done changeable architecture
Because of owner = address(0) at the end of the constructor, there is no way to fix it, so the owner’s authority can be maintained.
[Reffrence](https://code4rena.com/reports/2023-03-neotokyo#l-09-in-the-constructor-there-is-no-return-of-incorrect-address-identification)
```solidity
File:  core/solidity/contracts/core/WUSDA.sol
48    constructor(address _usdaToken, string memory _name, string memory _symbol) ERC20(_name, _symbol) ERC20Permit(_name) {
    USDA = _usdaToken;
  }
       
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/WUSDA.sol#L48-L50



## [L‑09] Signature use at deadlines should be allowed
According to [EIP-2612](https://github.com/ethereum/EIPs/blob/71dc97318013bf2ac572ab63fab530ac9ef419ca/EIPS/eip-2612.md?plain=1#L58), signatures used on exactly the deadline timestamp are supposed to be allowed. While the signature may or may not be used for the exact EIP-2612 use case (transfer approvals), for consistency's sake, all deadlines should follow this semantic. If the timestamp is an expiration rather than a deadline, consider whether it makes more sense to include the expiration timestamp as a valid timestamp, as is done for deadlines.


```solidity
File:  core/solidity/contracts/governance/GovernorCharlie.sol
560       return (whitelistAccountExpirations[_account] > block.timestamp);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/governance/GovernorCharlie.sol#L560

```solidity
File:  core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol
59       if (block.timestamp > _updatedAt + stalePriceDelay) _stale = true;  
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L59



## [L-10] Some ERC20 tokens should need to approve(0) first
Some tokens (like USDT) do not work when changing the allowance from an existing non-zero allowance value. They must first be approved by zero and then the actual allowance must be approved.

```solidity
File:  core/solidity/contracts/core/Vault.sol
321       IERC20(_token).approve(address(_booster), _amount);
```
https://github.com/code-423n4/2023-07-amphora/blob/41cf810b02a150bb168698ff1eeb1f768d6bdcb0/core/solidity/contracts/core/Vault.sol#L321

### Recommended Mitigation Steps
Approve with a zero amount first before setting the actual amount.

