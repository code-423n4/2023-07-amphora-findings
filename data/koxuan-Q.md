### [L&#x2011;1] ERC20 tokens that do not implement optional decimals method cannot be used

Underlying token that does not implement optional decimals method cannot be used

 > [EIP-20](https://eips.ethereum.org/EIPS/eip-20#decimals) OPTIONAL - This method can be used to improve usability, but interfaces and other contracts MUST NOT expect these values to be present.

*Instances (7)*:
```solidity
File: /core/solidity/contracts/core/VaultController.sol

341:    uint8 _tokenDecimals = IERC20Metadata(_tokenAddress).decimals();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L341

```solidity
File: /core/solidity/contracts/periphery/oracles/CTokenOracle.sol

25:    uint256 _underlyingDecimals = cETH_ADDRESS == _cToken ? 18 : IERC20Metadata(cToken.underlying()).decimals();

28:    div = 10 ** (18 + _underlyingDecimals - cToken.decimals());

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L25

```solidity
File: /core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol

50:    BASE_TOKEN_DECIMALS = IERC20Metadata(BASE_TOKEN).decimals();

51:    QUOTE_TOKEN_DECIMALS = IERC20Metadata(QUOTE_TOKEN).decimals();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L50

```solidity
File: /core/solidity/scripts/fakes/MintableToken.sol

20:  function decimals() public view virtual override returns (uint8) {

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/scripts/fakes/MintableToken.sol#L20

```solidity
File: /core/solidity/interfaces/periphery/ICToken.sol

9:  function decimals() external view returns (uint8 _decimals);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/periphery/ICToken.sol#L9


### [L&#x2011;2] Divide before Multiplication

Unnecessary loss of precision caused by divide before multiplication

*Instances (4)*:
```solidity
File: /core/solidity/contracts/core/AMPHClaimer.sol

196:    uint256 _bar = (_distributedAmph * 1e12) / (BASE_SUPPLY_PER_CLIFF * _FIFTY);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L196

```solidity
File: /core/solidity/contracts/utils/UFragments.sol

67:  uint256 public _totalGons; // = INITIAL_FRAGMENTS_SUPPLY * 10**48;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L67

```solidity
File: /core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol

85:    _e18Amount = (_decimals > 18) ? _amount / (10 ** (_decimals - 18)) : _amount * (10 ** (18 - _decimals));

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L85

```solidity
File: /core/solidity/contracts/governance/GovernorCharlie.sol

350:    (bool _success, /*bytes memory returnData*/ ) = _target.call{value: _value}(_callData);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L350


### [L&#x2011;3] _safeMint() should be used rather than _mint()

_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function

*Instances (9)*:
```solidity
File: /core/solidity/contracts/core/USDA.sol

104:    _mint(_target, _susdAmount);

163:    _mint(_msgSender(), _susdAmount);

167:  function _mint(address _target, uint256 _amount) internal {

225:    _mint(_target, _amount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L104

```solidity
File: /core/solidity/contracts/core/WUSDA.sol

216:    _mint(_to, _wusdaAmount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L216

```solidity
File: /core/solidity/contracts/governance/AmphoraProtocolToken.sol

16:    _mint(_account, _initialSupply);

21:    _mint(_dst, _rawAmount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L16

```solidity
File: /core/solidity/scripts/fakes/FakeBaseRewardPool.sol

143:    _mint(msg.sender, 100_000 ether);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/scripts/fakes/FakeBaseRewardPool.sol#L143

```solidity
File: /core/solidity/scripts/fakes/MintableToken.sol

17:    _mint(to, amount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/scripts/fakes/MintableToken.sol#L17


### [N&#x2011;4] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

*Instances (2)*:
```solidity
File: /core/solidity/contracts/core/USDA.sol

21:  bytes32 public constant VAULT_CONTROLLER_ROLE = keccak256('VAULT_CONTROLLER');

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L21

```solidity
File: /core/solidity/contracts/governance/GovernorCharlie.sol

23:  bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L23


### [N&#x2011;5] Lines are too long

Recommended by solidity docs to keep lines to 120 characters or lesser

*Instances (17)*:
```solidity
File: /core/solidity/contracts/core/AMPHClaimer.sol

213:      // all cliffs start when a certain amount of CRV is accumulated and finish when a certain amount is reached, this is the start of the current cliff

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L213

```solidity
File: /core/solidity/contracts/core/USDA.sol

83:  /// the calculations for deposit mimic the calculations done by mint in the ampleforth contract, simply with the susd transfer

169:    // the gonbalances of the sender is in gons, therefore we must multiply the deposit amount, which is in fragments, by gonsperfragment

173:    // and totalgons of course is in gons, and so we multiply amount by gonsperfragment to get the amount of gons we must add to totalGons

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L83

```solidity
File: /core/solidity/contracts/core/VaultController.sol

918:  /// @notice Returns the increase amount of the interest factor. Accrues interest to borrowers and distribute it to USDA holders

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L918

```solidity
File: /core/solidity/contracts/governance/GovernorCharlie.sol

28:  /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed

31:  /// @notice The number of votes in support of a proposal required in order for an emergency quorum to be reached and for a vote to succeed

681:   * @notice Governance function for setting the whitelistGuardian. WhitelistGuardian can cancel proposals from whitelisted addresses

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L28

```solidity
File: /core/solidity/scripts/Deploy.s.sol

80:    new VaultController(IVaultController(address(0)), _tokens, IAMPHClaimer(_amphClaimerAddress), _vaultDeployer, 0.01e18, _deployVars.booster, 0.005e18); // Nonce + 3

85:    new AMPHClaimer(address(_vaultController), IERC20(address(_amphToken)), _deployVars.cvxAddress, _deployVars.crvAddress, cvxRewardFee, crvRewardFee); // Nonce + 4

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/scripts/Deploy.s.sol#L80

```solidity
File: /core/solidity/scripts/DeployGoerli.s.sol

98:    new ChainlinkOracleRelay(address(wbtc), 0xA39434A63A52E749F02807ae27335515BA4b07F7, 10_000_000_000, 1, chainlinkStalePriceDelay);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/scripts/DeployGoerli.s.sol#L98

```solidity
File: /core/solidity/scripts/RegisterToken.s.sol

24:    /// To find the poolId of a specifil lp token check more details here: https://linear.app/defi-wonderland/issue/AMP-26#comment-fe3ec6f2

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/scripts/RegisterToken.s.sol#L24

```solidity
File: /core/solidity/interfaces/core/IUSDA.sol

132:  /// the calculations for deposit mimic the calculations done by mint in the ampleforth contract, simply with the susd transfer

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol#L132

```solidity
File: /core/solidity/interfaces/core/IVaultController.sol

447:  /// @notice Returns the increase amount of the interest factor. Accrues interest to borrowers and distribute it to USDA holders

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol#L447

```solidity
File: /core/solidity/interfaces/governance/IGovernorCharlie.sol

90:  /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed

93:  /// @notice The number of votes in support of a proposal required in order for an emergency quorum to be reached and for a vote to succeed

347:   * @notice Governance function for setting the whitelistGuardian. WhitelistGuardian can cancel proposals from whitelisted addresses

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IGovernorCharlie.sol#L90


### [N&#x2011;6] delete keyword can be used instead of setting to 0

It's clearer and reflects the intention of the programmer

*Instances (4)*:
```solidity
File: /core/solidity/contracts/core/VaultController.sol

262:      _collateral.totalDeposited = 0;

352:      _collateral.poolId = 0;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L262

```solidity
File: /core/solidity/scripts/fakes/FakeBaseRewardPool.sol

98:    rewards[_account].rewardsToPay = 0;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/scripts/fakes/FakeBaseRewardPool.sol#L98

```solidity
File: /core/solidity/scripts/fakes/FakeVirtualRewardsPool.sol

37:    rewards[msg.sender].rewardsToPay = 0;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/scripts/fakes/FakeVirtualRewardsPool.sol#L37


### [N&#x2011;7] Variable names don't follow the Solidity style guide

 Constant variable should be all caps  each word should use all capital letters, jith underscores separating each word (CONSTANT_CASE)

*Instances (10)*:
```solidity
File: /core/solidity/contracts/utils/UFragments.sol

78:  uint8 public constant decimals = uint8(DECIMALS);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L78

```solidity
File: /core/solidity/contracts/periphery/oracles/CTokenOracle.sol

12:  address public constant cETH_ADDRESS = 0x4Ddc2D193948926D02f9B1fE9e1daa0718270ED5;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L12

```solidity
File: /core/solidity/contracts/periphery/oracles/OracleRelay.sol

8:  address public constant wETH_ADDRESS = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L8

```solidity
File: /core/solidity/scripts/Deploy.s.sol

47:  uint256 public constant initialAmphSupply = 100_000_000 ether;

49:  uint256 public constant cvxRewardFee = 0.02 ether;

50:  uint256 public constant crvRewardFee = 0.01 ether;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/scripts/Deploy.s.sol#L47

```solidity
File: /core/solidity/scripts/DeployGoerli.s.sol

45:  address public constant wethGoerli = 0xB4FBF271143F4FBf7B91A5ded31805e42b2208d6;

46:  address public constant linkGoerli = 0x326C977E6efc84E512bB9C30f76E30c160eD06FB;

47:  address public constant ethUSD = 0xD4a33860578De61DBAbDc8BFdb98FD742fA7028e;

48:  address public constant linkUSD = 0x48731cF7e84dc94C5f84577882c14Be11a5B7456;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/scripts/DeployGoerli.s.sol#L45


### [N&#x2011;8] Constants in comparisons should appear on the left side

Constants should appear on the left side of comparisons, to avoid accidental assignment

*Instances (54)*:
```solidity
File: /core/solidity/contracts/core/AMPHClaimer.sol

44:  /// @dev Percentage of rewards taken in CVX (1e18 == 100%)

47:  /// @dev Percentage of rewards taken in CRV (1e18 == 100%)

152:    if (_total == 0) return 0;

169:    if (_crvTotalRewards == 0 || _amphBalance == 0) return (0, 0, 0);

169:    if (_crvTotalRewards == 0 || _amphBalance == 0) return (0, 0, 0);

180:    if (_amphByCrv == 0) return (0, 0, 0);

203:    if (_tokenAmountToSend == 0) return 0;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L44

```solidity
File: /core/solidity/contracts/core/USDA.sol

84:  /// 'fragments' are the units that we see, so 1000 fragments == 1000 USDA

102:    if (_susdAmount == 0) revert USDA_ZeroAmount();

148:    if (reserveAmount == 0) revert USDA_EmptyReserve();

149:    if (_susdAmount == 0) revert USDA_ZeroAmount();

162:    if (_susdAmount == 0) revert USDA_ZeroAmount();

183:    if (_susdAmount == 0) revert USDA_ZeroAmount();

203:    if (_susdAmount == 0) revert USDA_ZeroAmount();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L84

```solidity
File: /core/solidity/contracts/core/Vault.sol

90:    if (CONTROLLER.tokenId(_token) == 0) revert Vault_TokenNotRegistered();

91:    if (_amount == 0) revert Vault_AmountZero();

118:    if (CONTROLLER.tokenId(_tokenAddress) == 0) revert Vault_TokenNotRegistered();

141:    if (_poolId == 0) revert Vault_TokenCanNotBeStaked();

142:    if (balances[_tokenAddress] == 0) revert Vault_TokenZeroBalance();

171:      if (_collateralInfo.tokenId == 0) revert Vault_TokenNotRegistered();

235:    if (CONTROLLER.tokenId(_tokenAddress) == 0) revert Vault_TokenNotRegistered();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L90

```solidity
File: /core/solidity/contracts/core/VaultController.sol

68:  /// @dev The initial borrowing fee (1e18 == 100%)

70:  /// @dev The fee taken from the liquidator profit (1e18 == 100%)

257:      if (_tokenId == 0) revert VaultController_WrongCollateralAddress();

392:    if (_collateral.tokenId == 0) revert VaultController_TokenNotRegistered();

625:    if (_tokensToLiquidate == 0) revert VaultController_LiquidateZeroTokens();

627:    if (tokenAddressCollateralInfo[_assetAddress].tokenId == 0) revert VaultController_TokenNotRegistered();

652:    if (_tokensToLiquidate == 0) revert VaultController_LiquidateZeroTokens();

654:    if (tokenAddressCollateralInfo[_assetAddress].tokenId == 0) revert VaultController_TokenNotRegistered();

871:      if (_collateral.ltv == 0) continue;

877:      if (_balance == 0) continue;

880:      if (_rawPrice == 0) continue;

899:      if (_collateral.ltv == 0) continue;

905:      if (_balance == 0) continue;

908:      if (_rawPrice == 0) continue;

933:    if (_timeDifference == 0) return 0;

1017:    if (_collateral.tokenId == 0) revert VaultController_TokenNotRegistered();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L68

```solidity
File: /core/solidity/contracts/utils/ExponentialNoError.sol

94:    return _value.mantissa == 0;

171:    if (_a == 0 || _b == 0) return 0;

171:    if (_a == 0 || _b == 0) return 0;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ExponentialNoError.sol#L94

```solidity
File: /core/solidity/contracts/periphery/CurveMaster.sol

26:    if (_value == 0) revert CurveMaster_ZeroResult();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L26

```solidity
File: /core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol

66:    if (_stalePriceDelay == 0) revert ChainlinkOracle_ZeroAmount();

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L66

```solidity
File: /core/solidity/contracts/governance/GovernorCharlie.sol

168:    if (quorumVotes == 0) revert GovernorCharlie_NotActive();

176:    if (_targets.length == 0) revert GovernorCharlie_NoActions();

346:    if (bytes(_signature).length == 0) _callData = _data;

474:    else if (_proposal.eta == 0) return ProposalState.Succeeded;

543:    if (_support == 0) _proposal.againstVotes = _proposal.againstVotes + _votes;

544:    else if (_support == 1) _proposal.forVotes = _proposal.forVotes + _votes;

545:    else if (_support == 2) _proposal.abstainVotes = _proposal.abstainVotes + _votes;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L168

```solidity
File: /core/solidity/interfaces/core/IAMPHClaimer.sol

72:  /// @notice Percentage of rewards taken in CVX (1e18 == 100%)

75:  /// @notice Percentage of rewards taken in CRV (1e18 == 100%)

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IAMPHClaimer.sol#L72

```solidity
File: /core/solidity/interfaces/core/IUSDA.sol

133:  /// 'fragments' are the units that we see, so 1000 fragments == 1000 USDA

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol#L133

```solidity
File: /core/solidity/interfaces/core/IVaultController.sol

265:  /// @notice The initial borrowing fee (1e18 == 100%)

268:  /// @notice The fee taken from the liquidator profit (1e18 == 100%)

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IVaultController.sol#L265