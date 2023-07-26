### QA Report Issues List

- [x] **Low 01** → The `execute()` function is payable and has no control of the `msg.value` value, this may cause the user to lose Money
- [x] **Low 02** → Lack of price oracle output validation can result in wrong or stale price being used in UniswapV3OracleRelay.sol
- [x] **Low 03** → There is a risk that a user with a high governance power will not be able to bid with `propose()`
- [x] **Low 04** → Project Upgrade and Stop Scenario should be
- [x] **Low 05** → Project has  security risk from DAO attack using proposal
- [x] **Low 06** → Migrating with "_migrateCollateralsFrom()"may result in loss of user funds
- [x] **Low 07** → Missing Event for  initialize
- [x] **Low 08**→ If onlyOwner runs `renounceOwnership()` in the `AMPHClaimer` contract, the contract may become unavailable
- [x] **Non-Critical 09**→ Unused Import

### [Low-01] The `execute()` function is payable and has no control of the `msg.value` value, this may cause the user to lose Money

The `msg.value` value is sent to the contact with the `execute()` function.

but the following code is used to find the msg.value ;
`this.executeTransaction{value: _proposal.values[_i]}(`

Also in the for loop the msg.value is sent too many times, it has no control over it


```solidity
core/solidity/contracts/governance/GovernorCharlie.sol:
  308     */
  309:   function execute(uint256 _proposalId) external payable override {
  310:     if (state(_proposalId) != ProposalState.Queued) revert GovernorCharlie_ProposalNotQueued();
  311:     Proposal storage _proposal = proposals[_proposalId];
  312:     _proposal.executed = true;
  313:     for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {
  314:       this.executeTransaction{value: _proposal.values[_i]}(
  315:         _proposal.targets[_i], _proposal.values[_i], _proposal.signatures[_i], _proposal.calldatas[_i], _proposal.eta
  316:       );
  317:     }
  318:     emit ProposalExecutedIndexed(_proposalId);
  319:     emit ProposalExecuted(_proposalId);
  320:   }

```


### [Low-02] Lack of price oracle output validation can result in wrong or stale price being used in UniswapV3OracleRelay.sol

## Summary

Stale price and heavily outdated price can be used in Uniswap TWAP oracle

## Vulnerability Detail

Price oracle is important because it the price oracle report wrong price or stale price, the caller of the function flash to get the price at discount while not paying the token beneficiary properly

```solidity

core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol:
  78  
  79:   function _getPriceFromUniswap(uint32 _seconds, uint128 _amount) internal view virtual returns (uint256 _price) {
  80:     (int24 _arithmeticMeanTick,) = OracleLibrary.consult(address(POOL), _seconds);
  81:     _price = OracleLibrary.getQuoteAtTick(_arithmeticMeanTick, _amount, BASE_TOKEN, QUOTE_TOKEN);
  82:   }

```

the code tries to query the price with arithmeticMeanTic based on po.period, however, the price returned from this query method can be severely outdated and stale.

According to https://docs.uniswap.org/concepts/protocol/oracle

> Historical data is stored as an array of observations. At first, each pool tracks only a single observation, overwriting it as blocks elapse. This limits how far into the past users may access data. However, any party willing to pay the transaction fees may increase the number of tracked observations (up to a maximum of 65535), expanding the period of data availability to ~9 days or more.


## Recommendation

We recommend the validate the price returned from Uniswap V3 oracle to avoid stale price usage.

### [Low-03] There is a risk that a user with a high governance power will not be able to bid with `propose()`

Centralization risk in the DOA mechanism is that the people who can submit proposals must be on the whitelist, which is contrary to the essence of the DAO as it carries the risk of a user not being able to submit a proposal in the DAO even if he has a very high stake.

We should point out that this problem is beyond the centrality risk and is contrary to the functioning of the DAO. Because a user who has a governance token to be active in the DAO may not be able to bid if he is not included in the whitelist (there is no information in the documents about whether there is a warning that he cannot make a proposal if he is not on the whitelist)

There is no information on how, under what conditions and by whom the While List will be taken.

```solidity


core/solidity/contracts/governance/GovernorCharlie.sol:
  119     */
  120:   function propose(
  121:     address[] memory _targets,
  122:     uint256[] memory _values,
  123:     string[] memory _signatures,
  124:     bytes[] memory _calldatas,
  125:     string memory _description
  126:   ) public override returns (uint256 _proposalId) {
  127:     _proposalId = _propose(_targets, _values, _signatures, _calldatas, _description, false);
  128:   }
  129: 
  130:   /**
  131:    * @notice Function used to propose a new emergency proposal. Sender must have delegates above the proposal threshold
  132:    * @param _targets Target addresses for proposal calls
  133:    * @param _values Eth values for proposal calls
  134:    * @param _signatures Function signatures for proposal calls
  135:    * @param _calldatas Calldatas for proposal calls
  136:    * @param _description String description of the proposal
  137:    * @return _proposalId Proposal id of new proposal
  138:    */
  139:   function proposeEmergency(
  140:     address[] memory _targets,
  141:     uint256[] memory _values,
  142:     string[] memory _signatures,
  143:     bytes[] memory _calldatas,
  144:     string memory _description
  145:   ) public override returns (uint256 _proposalId) {
  146:     _proposalId = _propose(_targets, _values, _signatures, _calldatas, _description, true);
  147:   }

```


## Proof of concept

1- Alice receives a governance token to be actively involved in the project's DAO, participates in the voting, and also wants to present a proposal with the proposal in the project, but cannot do this because there is no whitelist.
2- There is no information about the whitelist conditions and how to whitelist Alice in the documents and NatSpec comments.



## Recommended Mitigation Steps

1- In the short term; Clarify the whitelist terms and processes and add them to the documents, and inform the users as a front-end warning in Governance token purchases.
2- In the Long Term; In accordance with the philosophy of the DAO, ensure that a proposal can be made according to the share weight.



### [Low-04] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

This can be done by adding the 'pause' architecture, which is included in many projects, to critical functions, and by authorizing the existing onlyOwner.

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol

### [Low-05] Project has  security risk from DAO attack using proposal

` GovernorCharlie.propose()` function used to propose a new proposal , Sender must have delegates above the proposal threshold.This functin is very critical because it builds an important task where DAO proposals are given, however it should be tightly controlled for a recent security concern, the proposal mechanism in the DAO must have limits, not everyone can read the code in proposal evaluation, the following hack is done using exactly this function, each proposal in It may even need to pass a minor inspection.


https://cointelegraph.com/news/attacker-hijacks-tornado-cash-governance-via-malicious-proposal


```solidity


core/solidity/contracts/governance/GovernorCharlie.sol:
  119     */
  120:   function propose(
  121:     address[] memory _targets,
  122:     uint256[] memory _values,
  123:     string[] memory _signatures,
  124:     bytes[] memory _calldatas,
  125:     string memory _description
  126:   ) public override returns (uint256 _proposalId) {
  127:     _proposalId = _propose(_targets, _values, _signatures, _calldatas, _description, false);
  128:   }

  
```

This vulnerability is very new and very difficult to prevent, but the importance of the project regarding this vulnerability could not be seen in the documents and NatSpec comments.

It is known that only Whitelist users can submit proposals, but whitelist terms etc. unknown, this problem persists.


## Proof of concept

A similar vulnerability has been analyzed in full detail here;
https://twitter.com/BlockSecTeam/status/1660214665429876740?t=bxFysCYHryV3Rx0yeGmmTg&s=33
https://docs.google.com/presentation/d/1QgYUmowhFxjuh18eV7zIlz0icr4MfnjKOJJ__yYVz-w/edit#slide=id.p

## Recommended Mitigation Steps

Projects should have a short audit of the proposals

https://a16zcrypto.com/posts/article/dao-governance-attacks-and-how-to-avoid-them/

### [Low-06] Migrating with "_migrateCollateralsFrom()"may result in loss of user funds

The ` VaultController. _migrateCollateralsFrom()` function defines the migrated.

```solidity
core/solidity/contracts/core/VaultController.sol:
  250    /// @param _oldVaultController The address of the vault controller to take the information from
  251:   /// @param _tokenAddresses The addresses of the tokens we want to target
  252:   function _migrateCollateralsFrom(IVaultController _oldVaultController, address[] memory _tokenAddresses) internal {
  253:     uint256 _tokenId;
  254:     uint256 _tokensRegistered;
  255:     for (uint256 _i; _i < _tokenAddresses.length;) {
  256:       _tokenId = _oldVaultController.tokenId(_tokenAddresses[_i]);
  257:       if (_tokenId == 0) revert VaultController_WrongCollateralAddress();
  258:       _tokensRegistered++;
  259: 
  260:       CollateralInfo memory _collateral = _oldVaultController.tokenCollateralInfo(_tokenAddresses[_i]);
  261:       _collateral.tokenId = _tokensRegistered;
  262:       _collateral.totalDeposited = 0;
  263: 
  264:       enabledTokens.push(_tokenAddresses[_i]);
  265:       tokenAddressCollateralInfo[_tokenAddresses[_i]] = _collateral;
  266: 
  267:       unchecked {
  268:         ++_i;
  269:       }
  270:     }
  271:     tokensRegistered += _tokensRegistered;
  272: 
  273:     emit CollateralsMigratedFrom(_oldVaultController, _tokenAddresses);
  274:   }


```
However, it has some design vulnerabilities.
1- Best practice, many projects use upgradable pattern instead of migrate, using a more war-tested method is more accurate in terms of security. Upgradability allows for making changes to contract logic while preserving the state and user funds. Migrating contracts can introduce additional risks, as the new contract may not have the same level of security or functionality. Consider implementing an upgradability pattern, such as using proxy contracts or modular design, to allow for safer upgrades without compromising user funds.



2- There may be user losses due to the funds remaining in the old safe, there is no control regarding this.



## Recommended Mitigation Steps

To mitigate this risk, you should implement appropriate measures to handle user funds during migration. This could involve implementing mechanisms such as time-limited withdrawal periods or providing clear instructions and notifications to users about the migration process, ensuring they have the opportunity to withdraw their funds from the old vault before the migration occurs.


### [Low-07] Missing Event for  initialize

**Context:**
```solidity

core/solidity/contracts/core/USDA.sol:
  56  
  57:   constructor(IERC20 _sUSDAddr) UFragments('USDA Token', 'USDA') {
  58:     sUSD = _sUSDAddr;
  59:   }

core/solidity/contracts/core/Vault.sol:
  65    /// @param _crv Address of CRV token
  66:   constructor(uint96 _id, address _minter, address _controllerAddress, IERC20 _cvx, IERC20 _crv) {
  67:     vaultInfo = VaultInfo(_id, _minter);
  68:     CONTROLLER = IVaultController(_controllerAddress);
  69:     CVX = ICVX(address(_cvx));
  70:     CRV = _crv;
  71:   }

core/solidity/contracts/core/VaultDeployer.sol:
  17    /// @param _crv The address of the CRV token
  18:   constructor(IERC20 _cvx, IERC20 _crv) payable {
  19:     CVX = _cvx;
  20:     CRV = _crv;
  21:   }

core/solidity/contracts/core/WUSDA.sol:
  47    /// @param _symbol The wUSDA ERC20 symbol.
  48:   constructor(address _usdaToken, string memory _name, string memory _symbol) ERC20(_name, _symbol) ERC20Permit(_name) {
  49:     USDA = _usdaToken;
  50:   }

```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes
Issuing event-emit during initialization is a detail that many projects skip

**Recommendation:**
Add Event-Emit

### [Low-08] If onlyOwner runs `renounceOwnership()` in the `AMPHClaimer` contract, the contract may become unavailable


```solidity
core/solidity/contracts/core/AMPHClaimer.sol:
  118    /// @param _newVaultController The new vault controller
  119:   function changeVaultController(address _newVaultController) external override onlyOwner {
  120      vaultController = IVaultController(_newVaultController);

  127    /// @param _amount The amount to recover
  128:   function recoverDust(address _token, uint256 _amount) external override onlyOwner {
  129      IERC20(_token).transfer(owner(), _amount);

  135    /// @param _newFee The new reward fee
  136:   function changeCvxRewardFee(uint256 _newFee) external override onlyOwner {
  137      cvxRewardFee = _newFee;

  143    /// @param _newFee The new reward fee
  144:   function changeCrvRewardFee(uint256 _newFee) external override onlyOwner {
  145      crvRewardFee = _newFee;


```

the `onlyOwner` authority here is very important for the contract, but the `Ownable.sol` library imported with the import has the `renounceOwnership()` feature, in case the owner accidentally triggers this function, some functions can’t be run


https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol#L73


### Recommended Mitigation Steps

The solution to this is to overide and disable the renounceOwnership() function as implemented in many contracts in his project, it is important to include this code in the contract


```solidity
 function renounceOwnership() public payable override onlyOwner {
        revert("Cannot renounce ownership");
    }
```

### [Non Critical-09] Unused Import

Some imports aren’t used inside the project

```solidity

core/solidity/contracts/core/AMPHClaimer.sol:
  9: import {IVault} from '@interfaces/core/IVault.sol’;

core/solidity/contracts/core/USDA.sol:
  13: import {Context} from '@openzeppelin/contracts/utils/Context.sol’;

```


