# Low/NC issues

## [L-01] quorumVotes can be setted to 0
### Impact 
It is possible for `quorumVotes` to be set to zero by the government.

```solidity
function setQuorumVotes(uint256 _newQuorumVotes) external override onlyGov {
    //@audit _newQuorumVotes != 0
    uint256 _oldQuorumVotes = quorumVotes;
    quorumVotes = _newQuorumVotes;

    emit NewQuorum(_oldQuorumVotes, quorumVotes);
  }
```
  
If this happens, it will block the creation of new proposals.

```solidity
    // Reject proposals before initiating as Governor
    if (quorumVotes == 0) revert GovernorCharlie_NotActive();
```

### Recommended Mitigation Steps
Add additional check for that: 

```solidity
function setQuorumVotes(uint256 _newQuorumVotes) external override onlyGov {
    if(_newQuorumVotes == 0) revert ZeroQuorumVotes();

    uint256 _oldQuorumVotes = quorumVotes;
    quorumVotes = _newQuorumVotes;

    emit NewQuorum(_oldQuorumVotes, quorumVotes);
  }
```

## [L-02] Check if msg.sender is equal to signatory
### Impact 
From the provided signature by `msg.sender`, the address of the `signatory` (the user who signed this signature) is obtained. The address of the `signatory` is checked to confirm if it is equal to `address(0)`, but it is never checked whether `msg.sender == signatory`.

```solidity
address _signatory = ecrecover(_digest, _v, _r, _s);
if (_signatory == address(0)) revert GovernorCharlie_InvalidSignature();
//@note check if msg.sender == signatory
```

msg.sender can use someone else's signature to vote on a given proposal.

### Recommended Mitigation Steps
Add additional check for that

## [L-03] Missed access modifier for deployVault function
### Impact 
The modifier for vault controller access for the `deployVault` function is missing, and if a user calls this function, they will create a vault but will not be registered in the system (not included in VaultController). Users can create such malfunctioning vaults and get confused.

### Recommended Mitigation Steps
Add additional moddifer for VaultController access.


## [L-04] CurveMaster address should be set as the default in the constructor
### Impact 
The CurveMaster address should be initialized in the constructor of VaultController. If a controller with the CurveMaster address not initialized(equal to address(0)) is added to the list of `USDA`, the `_payInterest()` function will revert when is called in the `paysInterest` modifier.

```solidity
  function addVaultController(address _vaultController) external onlyOwner {
    _vaultControllers.add(_vaultController); 
```
 
The problem:
`int256 _intCurveVal = curveMaster.getValueAt(address(0x00), _reserveRatio);`

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L940

## [NC-01] Wrong event argument
### Impact 
The wrong event argument is used in the `_deposit` function when emitting the `Deposit` event. It is expected to be emitted `from` address, not the target address.

`emit Deposit(_target, _susdAmount); `

Interface comment:
```solidity
  /**
   * @notice Emitted when a deposit is made
   * @param _from The address which made the deposit
   * @param _value The value deposited
   */
  event Deposit(address indexed _from, uint256 _value);
```

It is the same problem with the `Withdraw` event in `_withdraw` function. 

## [NC-02] Not handled return value for EnumerableSet.AddressSet
Storage variable `_vaultControllers` is `EnumerableSet.AddressSet`. Add and Remove function of`EnumerableSet.AddressSet` return bool. It is not handled return value of `add()` and `remove()` in `addVaultController`, `removeVaultController` and `removeVaultControllerFromList` functions.

## [NC-03] Events ProposalCreated and ProposalCreatedIndexed are same
Events `ProposalCreated` and `ProposalCreatedIndexed` are same and can be used only once for emmiting the desired information.
