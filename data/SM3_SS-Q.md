


## Low Risk Issues

|  NO  |Issue  | Instances |
|:----:|:-----:|:---------:|
| L-01 | External call recipient may consume all transaction gas |    1    |
| L-02 | Allowed fees/rates should be capped by smart contracts  |    3     |
| L-03 | address shouldn't be hard-coded   |     4     |
| L-04 | State variables not capped at reasonable values   |     3     |
| L-05 | Consider implementing two-step procedure for updating protocol addresses   |     6    |
| L-06 | Burn functions should be protected with a modifier   |     12     |
| L-07 | Using zero as a parameter   |   2    |
| L-08 | Project has vulnarable NPM dependency version   |     2     |
| L-09 | Use safeApprove instead of approve   |    3     |
| L-10 | Unsafe ERC20 operation(s)   |    4     |
| L-11 | Array lengths not checked   |    2     |
| L-12 | Emitting storage values instead of the memory one.   |     16     |

   

## [L‑01] External call recipient may consume all transaction gas

There is no limit specified on the amount of gas used, so the recipient can use up all of the transaction's gas, causing it to revert. Use addr.call{gas: <amount>}("") or this library instead.


```solidity
file:  contracts/governance/GovernorCharlie.sol

350    (bool _success, /*bytes memory returnData*/ ) = _target.call{value: _value}(_callData);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L350


## [L‑02] Allowed fees/rates should be capped by smart contracts

Fees/rates should be required to be below 100%, preferably at a much lower limit, to ensure users don't have to monitor the blockchain for changes prior to using the protocol

Here's an example to illustrate this concept using a simplified lending protocol:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract LendingProtocol {
    uint256 public constant MAX_FEE_PERCENT = 1; // 1% maximum fee allowed

    function lend(uint256 amount) external payable {
        uint256 fee = (amount * MAX_FEE_PERCENT) / 100;
        require(msg.value >= fee, "Insufficient fee");
        
        // Perform lending logic and transfer funds
        // ...

        // Refund any excess funds to the sender
        if (msg.value > fee) {
            payable(msg.sender).transfer(msg.value - fee);
        }
    }
}
```

```solidity
file:  contracts/core/AMPHClaimer.sol

136      function changeCvxRewardFee(uint256 _newFee) external override onlyOwner {
    cvxRewardFee = _newFee;

    emit ChangedCvxRewardFee(_newFee);
  }

136      function changeCrvRewardFee(uint256 _newFee) external override onlyOwner {
    crvRewardFee = _newFee;

    emit ChangedCrvRewardFee(_newFee);
  }
```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L136-L140

```solidity
file:   contracts/core/VaultController.sol

319     function changeProtocolFee(uint192 _newProtocolFee) external override onlyOwner {
    if (_newProtocolFee >= 1e18) revert VaultController_FeeTooLarge();
    protocolFee = _newProtocolFee;
    emit NewProtocolFee(_newProtocolFee);
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L319-L324

## [L-03] address shouldn't be hard-coded

It is often better to declare addresses as immutable, and assign them via constructor arguments. This allows the code to remain the same across deployments on different networks, and avoids recompilation when addresses need to change.

```solidity
file:   contracts/periphery/oracles/CTokenOracle.sol

12      address public constant cETH_ADDRESS = 0x4Ddc2D193948926D02f9B1fE9e1daa0718270ED5;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L12

```solidity
file:  contracts/periphery/oracles/OracleRelay.sol

8      address public constant wETH_ADDRESS = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol#L8

```solidity
file;   contracts/periphery/oracles/CurveRegistryUtils.sol

8    ICurveAddressesProvider public constant CURVE_ADDRESSES_PROVIDER =
    ICurveAddressesProvider(0x0000000022D53366457F9d5E68Ec105046FC4383);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CurveRegistryUtils.sol#L8-L9

```solidity
file:  contracts/periphery/oracles/WstEthOracle.sol

10    IWStETH public constant WSTETH = IWStETH(0x7f39C581F595B53c5cb19bD0b3f8dA6c935E2Ca0);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/WstEthOracle.sol#L10

## [L‑04] State variables not capped at reasonable values

Consider adding minimum/maximum value checks to ensure that the state variables below can never be used to excessively harm users, including via griefing

```solidity
file:  contracts/core/USDA.sol

106    reserveAmount += _susdAmount;

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L106

```solidity
file:  contracts/core/VaultController.sol

271    tokensRegistered += _tokensRegistered;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L271

```solidity
file:  contracts/periphery/oracles/CbEthEthOracle.sol

76     virtualPrice = _virtualPrice;

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol#L76


## [L‑05] Consider implementing two-step procedure for updating protocol addresses

A copy-paste error or a typo may end up bricking protocol functionality, or sending tokens to an address with no known private key. Consider implementing a two-step procedure for updating protocol addresses, where the recipient is set as pending, and must 'accept' the assignment by making an affirmative call. A straight forward way of doing this would be to have the target contracts implement EIP-165, and to have the 'set' functions ensure that the recipient is of the right interface type.

 implementing a two-step procedure for updating protocol addresses to prevent critical errors like copy-paste mistakes or typos that could cause severe issues or lock up funds. The proposed approach involves setting the new address as "pending" first and then requiring the recipient to explicitly "accept" the assignment by making a positive confirmation call. This two-step process helps ensure that only verified and compatible addresses are used in the protocol.

 Let's explain this with an example:

 ```solidity
 
 // SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

contract ExampleProtocol {
    address public pendingTokenContract;
    address public tokenContract;

    // Function to initiate the update of the token contract address
    function setPendingTokenContract(address _newTokenContract) external {
        pendingTokenContract = _newTokenContract;
    }

    // Function for the recipient to accept the new address
    function acceptTokenContract() external {
        require(pendingTokenContract != address(0), "No pending address");
        tokenContract = pendingTokenContract;
        pendingTokenContract = address(0); // Reset the pending address after acceptance
    }

    // Other protocol functions...
}

 ```

```solidity
file:   contracts/core/USDA.sol

63     function setPauser(address _pauser) external override onlyOwner {
    pauser = _pauser;

    emit PauserSet(_pauser);
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L63-L67
```solidity
file:

136     function changeCvxRewardFee(uint256 _newFee) external override onlyOwner {
      cvxRewardFee = _newFee;

     emit ChangedCvxRewardFee(_newFee);
  }

144     function changeCrvRewardFee(uint256 _newFee) external override onlyOwner {
    crvRewardFee = _newFee;

    emit ChangedCrvRewardFee(_newFee);
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol#L136-L140


```solidity
file:   contracts/core/VaultController.sol

319      function changeProtocolFee(uint192 _newProtocolFee) external override onlyOwner {
    if (_newProtocolFee >= 1e18) revert VaultController_FeeTooLarge();
    protocolFee = _newProtocolFee;
    emit NewProtocolFee(_newProtocolFee);
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L319-L324

```solidity
file:  contracts/periphery/oracles/CTokenOracle.sol

57     function changeAnchoredView(address _anchoredViewUnderlying) external onlyOwner {
    anchoredViewUnderlying = IOracleRelay(_anchoredViewUnderlying);
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L57-L59

```solidity
file:   contracts/periphery/CurveMaster.sol

31     function setVaultController(address _vaultMasterAddress) external override onlyOwner {
    address _oldCurveAddress = vaultControllerAddress;
    vaultControllerAddress = _vaultMasterAddress;

    emit VaultControllerSet(_oldCurveAddress, _vaultMasterAddress);
  }

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol#L31-L36

## [L-06] Burn functions should be protected with a modifier

By using the  modifier for the burn function, the contract ensures that only the specefic , who has the necessary authority, can initiate token burning, adding an extra layer of protection and control over the token supply management.


```solidity
file:  interfaces/core/IUSDA.sol
 
194    function burn(uint256 _susdAmount) external;

199    function vaultControllerBurn(address _target, uint256 _amount) external

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IUSDA.sol#L194

```solidity
file:  interfaces/core/IWUSDA.sol

78    function burn(uint256 _wusdaAmount) external returns (uint256 _usdaAmount);

86    function burnTo(address _to, uint256 _wusdaAmount) external returns (uint256 _usdaAmount);

91    function burnAll() external returns (uint256 _usdaAmount);

98    function burnAllTo(address _to) external returns (uint256 _usdaAmount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/core/IWUSDA.sol#L78

```solidity
file:    contracts/core/USDA.sol

182      function burn(uint256 _susdAmount) external override paysInterest onlyOwner

188      function _burn(address _target, uint256 _amount) internal 

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L182

```solidity
file:  contracts/core/WUSDA.sol

79     function burn(uint256 _wusdaAmount) external override returns (uint256 _usdaAmount)

90     function burnTo(address _to, uint256 _wusdaAmount) external override returns (uint256 _usdaAmount)

98     function burnAll() external override returns (uint256 _usdaAmount) 

109    function burnAllTo(address _to) external override returns (uint256 _usdaAmount) 

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L79

## [L-07] Using zero as a parameter

Taking 0 as a valid argument in Solidity without checks can lead to severe security issues. A historical example is the infamous 0x0 address bug where numerous tokens were lost. This happens because 0 can be interpreted as an uninitialized address, leading to transfers to the 0x0 address, effectively burning tokens. Moreover, 0 as a denominator in division operations would cause a runtime exception. It's also often indicative of a logical error in the caller's code. It's important to always validate input and handle edge cases like 0 appropriately. Use require() statements to enforce conditions and provide clear error messages to facilitate debugging and safer code.


```solidity
file:   contracts/core/VaultController.sol

576    _repay(_id, 0, true);  

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L576

```solidity
file:   contracts/periphery/oracles/EthSafeStableCurveOracle.sol

41      CRV_POOL.remove_liquidity(0, _amounts);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol#L41


## [L-08] Project has vulnarable NPM dependency version

Upgrade Openzeppelin versi,on to version 4.9.2 or higher. Check
https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts and
https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts-upgradeable for more information.

```solidity
file:  package.json
package.json
47         "@openzeppelin/contracts": "4.8.2",

48         "@openzeppelin/contracts-upgradeable": "4.8.1",

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/package.json#L47

## [L-09] Use safeApprove instead of approve

The approve function in ERC20 tokens is not safe to use. It is possible for an attacker to front-run the transaction and steal the tokens. Use safeApprove instead.

```solidity
file:   contracts/core/Vault.sol

212     CRV.approve(address(_amphClaimer), _takenCRV);

213     CVX.approve(address(_amphClaimer), _takenCVX);

312     IERC20(_token).approve(address(_booster), _amount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L212

## [L-10] Unsafe ERC20 operation(s)


```solidity
file:    contracts/core/Vault.sol

92       SafeERC20Upgradeable.safeTransferFrom(IERC20Upgradeable(_token), _msgSender(), address(this), _amount);

```

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L92

```solidity
file:   contracts/core/WUSDA.sol

215     IERC20(USDA).safeTransferFrom(_from, address(this), _usdaAmount);    

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol#L215

```solidity
file:  contracts/core/USDA.sol

103    sUSD.transferFrom(_msgSender(), address(this), _susdAmount);

206    sUSD.transferFrom(_msgSender(), address(this), _susdAmount);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol#L103


## [L‑11] Array lengths not checked

If the length of the arrays are not required to be of the same length, user operations may not be fully executed due to a mismatch in the number of items iterated over, versus the number of items provided in the second array

```solidity
file:   interfaces/governance/IGovernorCharlie.sol


164      function propose(
    address[] memory _targets,
    uint256[] memory _values,
    string[] memory _signatures,
    bytes[] memory _calldatas,
    string memory _description
  ) external returns (uint256 _proposalId);

181     function proposeEmergency(
    address[] memory _targets,
    uint256[] memory _values,
    string[] memory _signatures,
    bytes[] memory _calldatas,
    string memory _description
  ) external returns (uint256 _proposalId);


```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/interfaces/governance/IGovernorCharlie.sol#L164-L170


##  [L-12] Emitting storage values instead of the memory one.

Emitted values should not be read from storage again. Instead, the existing values from memory should be used.

```solidity
file:  contracts/governance/GovernorCharlie.sol

587   emit NewDelay(_oldTimelockDelay, proposalTimelockDelay);

598   emit NewEmergencyDelay(_oldEmergencyTimelockDelay, emergencyTimelockDelay);

609   emit VotingDelaySet(_oldVotingDelay, votingDelay);

620   emit VotingPeriodSet(_oldVotingPeriod, votingPeriod);

631   emit EmergencyVotingPeriodSet(_oldEmergencyVotingPeriod, emergencyVotingPeriod); 

642   emit ProposalThresholdSet(_oldProposalThreshold, proposalThreshold);

653   emit NewQuorum(_oldQuorumVotes, quorumVotes);

664   emit NewEmergencyQuorum(_oldEmergencyQuorumVotes, emergencyQuorumVotes);

688   emit WhitelistGuardianSet(_oldGuardian, whitelistGuardian);

699   emit OptimisticVotingDelaySet(_oldOptimisticVotingDelay, optimisticVotingDelay);

710   emit OptimisticQuorumVotesSet(_oldOptimisticQuorumVotes, optimisticQuorumVotes);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L587

```solidity
file:  contracts/utils/UFragments.sol

105    emit Transfer(address(this), address(0x0), _totalSupply);

286    emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);

301    emit Approval(msg.sender, _spender, _allowedFragments[msg.sender][_spender]);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L105

```solidity
file:  contracts/core/Vault.sol

150   emit Staked(_tokenAddress, balances[_tokenAddress]);

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol#L150

```solidity
file:   contracts/core/VaultController.sol

290    emit NewVault(_vaultAddress, vaultsMinted, _msgSender());

```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L290
