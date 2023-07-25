# Gas Report
**All gas tables are gotten from the repositories tests, and they do not implement the general design changes unless stated otherwise**
## General Design
1. For loops we should always write them as such
```solidity
for(uint256 i; i<cachedLength;) {
	// Logic
	unchecked { ++i;}
}
```
for example [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/USDA.sol#L48C4-L53C6) we don't use pre incrementing which is marginally cheaper then `i++`

2. When storing a storage variable that is being set with `keccak256()` we can compute the `keccak256()` and just put the hash in manually to save deployment gas for example [this](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/USDA.sol#L21) can be turned into this `bytes32  public  constant VAULT_CONTROLLER_ROLE =  0x995a5d6a7cac103b73a94cfaaf03645dcc7d6197e2cb553c21c01213f18592e9;`

3. If we are ever incrementing a counter value we should always pre increment it, for example [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L258C8-L258C8) we should also always wrap are counter variable increments in an `unchecked` box, because it is not feasible for them to ever overflow.
4.  We should not emit storage variables in event, we should cache the value and emit the cache for example [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L290C2-L290C2)
5. Due to the nature of the solidity compiler and how it compiles the opcodes, we should put functions that we expect our user to call most often at the top of the function session of the file, this will save some gas.

## Findings
1. The storage variables `cvxRewardFee` and `crvRewardFee` found [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/AMPHClaimer.sol#L45) can be packed into one storage slot because the theoretical max value for these numbers is `1e18` which is a 100% fee we can pack define them both as such
```solidity
  /// @dev Percentage of rewards taken in CVX (1e18 == 100%)
  uint128 public cvxRewardFee;
  /// @dev Percentage of rewards taken in CRV (1e18 == 100%)
  uint128 public crvRewardFee;
```
2. [Here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/Vault.sol#L89C3-L109C4) we can cache `balances[_token]` to save some gas when reading from it as such:
```solidity
 if (CONTROLLER.tokenId(_token) == 0) revert Vault_TokenNotRegistered();
    if (_amount == 0) revert Vault_AmountZero();
    SafeERC20Upgradeable.safeTransferFrom(IERC20Upgradeable(_token), _msgSender(), address(this), _amount);
    uint256 _balance = balances[_token];
    if (CONTROLLER.tokenCollateralType(_token) == IVaultController.CollateralType.CurveLPStakedOnConvex) {
      uint256 _poolId = CONTROLLER.tokenPoolId(_token);
      /// If it's type CurveLPStakedOnConvex then pool id can't be 0
      IBooster _booster = CONTROLLER.BOOSTER();
      if (isTokenStaked[_token]) {
        /// In this case the user's balance is already staked so we only stake the newly deposited amount
        _depositAndStakeOnConvex(_token, _booster, _amount, _poolId);
      } else {
        /// In this case the user's balance isn't staked so we stake the amount + his balance for the specific tokenv
        isTokenStaked[_token] = true;
        _depositAndStakeOnConvex(_token, _booster, _balance + _amount, _poolId);
      }
    }
    balances[_token] = _balance + _amount;
    CONTROLLER.modifyTotalDeposited(vaultInfo.id, _amount, _token, true);
    emit Deposit(_token, _amount);
```


3. In the `claimRewards` function found [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/Vault.sol#L164C2-L204C6)  we can also utilize unchecked math like the example below
```solidity
unchecked {
	_totalCvxReward -= _takenCVX;
	_totalCrvReward -= _takenCRV;
}
```
this is possible because of the nature of the claimable function _takenCVX and _takenCRV will never be greater then the total reward.

4. Due to the nature of the line above [this](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/Vault.sol#L286) we can wrap it an unchecked to save gas before if there is an underflow the `safeTransfer` will revert anyways as such 
```solidity
  function controllerTransfer(address _token, address _to, uint256 _amount) external override onlyVaultController {
    SafeERC20Upgradeable.safeTransfer(IERC20Upgradeable(_token), _to, _amount);
	unchecked{balances[_token] -= _amount;}
  }
```
5. Due to the above check [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/Vault.sol#L314) we can also wrap this in an unchecked
6. The [variables](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L60C3-L71C33) in the VaultController can be packed into less storage slots as such
```solidity
/// @dev Total number of minted vaults
uint64  public vaultsMinted;
/// @dev Total base liability
uint192  public totalBaseLiability;
/// @dev Total number of tokens registered
uint64  public tokensRegistered;
/// @dev The protocol's fee
uint64  public protocolFee;
/// @dev The initial borrowing fee (1e18 == 100%)
uint64  public initialBorrowingFee;
/// @dev The fee taken from the liquidator profit (1e18 == 100%)
uint64  public liquidationFee;
```
This structure packs all of these storage variables into two slots, and this is safe because the 5 uint64s will never go above the max uint64

7. This is a design change but im making it a finding becuase we should not use `vaultsMinted += 1` [here.](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L280) This is an extra SLOAD call that can be avoided, we should refactor it into `unchecked { ++vaultsMinted } `

8. The storage variables found [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L35) can be packed more efficiently, since the voting delay is using block numbers and not timestamps we can use uint32 to store it, this means the max delay would be 4294967295.
```solidity
mapping(uint256  =>  mapping(address  => Receipt)) public proposalReceipts;
mapping(uint256  => Proposal) public proposals;
mapping(address  =>  uint256) public latestProposalIds;
mapping(bytes32  =>  bool) public queuedTransactions;
mapping(address  =>  uint256) public whitelistAccountExpirations;
IAMPH public amph;
uint256  public quorumVotes;
uint256  public emergencyQuorumVotes;
uint256  public optimisticQuorumVotes;
uint32  public votingDelay;
uint32  public votingPeriod;
uint32  public emergencyVotingPeriod;
uint32  public emergencyTimelockDelay;
uint32  public optimisticVotingDelay;
uint32  public maxWhitelistPeriod;
uint32  public proposalTimelockDelay;
uint96  public proposalCount;
address  public whitelistGuardian;
uint256  public proposalThreshold;
uint256  public initialProposalId;
```

9. Emitting both the events [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L520C1-L521C74) can be a waste of gas we only need to emit one event and we can make it indexed, there are several other similar instances of this issue in this contract
10. The [struct](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/GovernanceStructs.sol#L4) `Proposal` can be optimized to use less slots as such, this because timestamps and the id can be safely stored in a smaller byte variable.
```solidity
struct Proposal {
  uint256 quorumVotes;
  uint256 forVotes;
  uint256 againstVotes;
  uint256 abstainVotes;
  address[] targets;
  uint256[] values;
  string[] signatures;
  bytes[] calldatas;
  uint96 id;
  address proposer;
  uint48 eta;
  uint48 startBlock;
  uint48 endBlock;
  uint48 delay;
  bool canceled;
  bool executed;
  bool emergency;
}
```
11.  The check [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L297) is redundant and we can remove it to save some gas, it is redundant because in the external function that calls the internal function it passed down the `eta` variable as such `uint256 _eta = block.timestamp + _proposal.delay;`making it impossible for this check to ever reject.

Gas Table with Implementation for finding 10, 11 and 12 (This table does not account for any general design changes that were recommended)
| Method | Avg Cost before changes | Avg Cost after changes | % Savings
|:----------| :----------| :----------| :----------| 
| execute | 49388 | 33573 | 38.13%|
| propose | 477389 | 403128 | 16.87%|
| proposeEmergency | 467633| 377432 | 21.35%|
| queue | 59881 | 39980 | 39.86%|

With emitting only one event as per the general design changes we can get `propose` to about 390k and `proposeEmergency` to about 365k

12. [Here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/interfaces/core/IVaultController.sol#L225) we can pack this variables into fewer slots as such to reduce costs
```solidity
struct  CollateralInfo {
uint256 cap;
uint256 totalDeposited;
IOracleRelay oracle;
CollateralType collateralType;
IBaseRewardPool crvRewardsContract;
uint56 tokenId;
uint64 ltv;
uint64 liquidationIncentive;
uint64 poolId;
uint8 decimals;
}
```
We can safely store the `ltv` and `liquidiationIncentive` in smaller variables because they will never be greater then 1e18, as well as for the Ids its not feasible to have an id larger then 72057594037927935 which is the largest uint56 possible.

For `updateRegisteredErc20` while the savings are significant the tests in the repo that calculate this are updating only one storage slot and its turning a zero into a non-zero, which is why the savings are so significant in this example

| Method | Avg Cost before changes | Avg Cost after changes | % Savings
|:----------| :----------| :----------| :----------| 
| registerErc20 | 186608 | 127491 | 37.64%|
| updateRegisteredErc20 | 61406 | 21812 | 95.16%|
| borrowUSDA | 233210 | 204111 | 13.3%|

13. Use uint256 when saving a value in memory
For example [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L868) we don't need to make `_i` a uint192, every uint by default is computed at 256 bits, so it costs extra gas to downgrade, because of this we can keep it at uint256.

14. Cache the length of the storage array
[Here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L868) we are also reading `enabledTokens.length` which is calling an SLOAD for every interval in the for loop, we can change this to cache the length as such
```solidity
uint256 len = enabledTokens.length;
for(uint256 _i; _i<len; ) {
	// Logic
	unchecked{++_i;}
}
```
15. Use `<` instead of `<=` when possible
For example [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/periphery/oracles/ChainlinkStalePriceLib.sol#L13) we can refactor this into ` if (_answer < 1) revert Chainlink_NegativePrice();` to save some gas.
16. Cache the storage variable array length
Found [here](https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L257C1-L259C64) while I have mentioned similar issues in the report several times this one is slightly different, saving the whole `Proposal` in storage is more optimal due to memory explosion, but we can still cache just the array length of which we are looping over to save some gas