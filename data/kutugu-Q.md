# Findings Summary

| ID     | Title                                                                                      | Severity |
| ------ | ------------------------------------------------------------------------------------------ | -------- |
| [L-01] | UFragments transferAllFrom should round up when calculating allowance value                | Low      |
| [L-02] | GovernorCharlie queue proposal time check is invalid and always success                    | Low      |
| [L-03] | UniswapV3Oracle obtains the price at a single tick that is susceptible to manipulation     | Low      |
| [L-04] | The failure of the user to repay after the pause of the protocol may lead to liquidation   | Low      |
| [L-05] | VaultController _getVaultBorrowingPower function possible exhaustes gas                    | Low      |
| [L-06] | The decimal of the oracle price may not be 18, causing vault to miscalculate               | Low      |
| [L-07] | If sUSD is depegged, The vault can be arbitraged                                           | Low      |

# Detailed Findings

# [L-01] UFragments transferAllFrom should round up when calculating value

## Description

```solidity
  function transferAllFrom(address _from, address _to) external validRecipient(_to) returns (bool _success) {
    uint256 _gonValue = _gonBalances[_from];
    uint256 _value = _gonValue / _gonsPerFragment;

    _allowedFragments[_from][msg.sender] = _allowedFragments[_from][msg.sender] - _value;

    delete _gonBalances[_from];
    _gonBalances[_to] = _gonBalances[_to] + _gonValue;

    emit Transfer(_from, _to, _value);
    return true;
  }
```

The allowance to be consumed should be rounded up. Since _gonsPerFragment is variable, it is possible that _gonValue will eventually divide by _gonsPerFragment with a remainder, so there will be a precision error in division, resulting in the reduced allowance being 1 lower than the actual allowance.
If _gonsPerFragment = 10000, _gonValue = 19999, then allowance consumed is only 1 instead of 2, and the user can transfer 19999 usda again later. This does not meet the owner's expectations.

## Recommendations

When calculating allowance _value, division should be rounded up, not down.

# [L-02] GovernorCharlie queue proposal time check is invalid and always success

## Description

```solidity
  function queue(uint256 _proposalId) external override {
    if (state(_proposalId) != ProposalState.Succeeded) revert GovernorCharlie_ProposalNotSucceeded();
    Proposal storage _proposal = proposals[_proposalId];
    uint256 _eta = block.timestamp + _proposal.delay;
    for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {
      if (
        queuedTransactions[keccak256(
          abi.encode(
            _proposal.targets[_i], _proposal.values[_i], _proposal.signatures[_i], _proposal.calldatas[_i], _eta
          )
        )]
      ) revert GovernorCharlie_ProposalAlreadyQueued();
      _queueTransaction(
        _proposal.targets[_i],
        _proposal.values[_i],
        _proposal.signatures[_i],
        _proposal.calldatas[_i],
        _eta,
        _proposal.delay
      );
    }
    _proposal.eta = _eta;
    emit ProposalQueuedIndexed(_proposalId, _eta);
    emit ProposalQueued(_proposalId, _eta);
  }

  function _queueTransaction(
    address _target,
    uint256 _value,
    string memory _signature,
    bytes memory _data,
    uint256 _eta,
    uint256 _delay
  ) internal returns (bytes32 _txHash) {
    if (_eta < (_getBlockTimestamp() + _delay)) revert GovernorCharlie_DelayNotReached();

    _txHash = keccak256(abi.encode(_target, _value, _signature, _data, _eta));
    queuedTransactions[_txHash] = true;

    emit QueueTransaction(_txHash, _target, _value, _signature, _data, _eta);
  }
```

The `queue` calls `_queueTransaction` passing `_eta = block.timestamp + _proposal.delay` and `_delay = _proposal.delay`, `_eta >= (_getBlockTimestamp() + _delay) = block.timestamp + _proposal.delay` will be checked in _queueTransaction. But the two conditions are exactly equal, so the check always succeeds.

```solidity
  function state(uint256 _proposalId) public view override returns (ProposalState _state) {
    if (proposalCount < _proposalId || _proposalId <= initialProposalId) revert GovernorCharlie_InvalidProposalId();
    Proposal storage _proposal = proposals[_proposalId];
    bool _whitelisted = isWhitelisted(_proposal.proposer);
    if (_proposal.canceled) return ProposalState.Canceled;
    else if (block.number <= _proposal.startBlock) return ProposalState.Pending;
    else if (block.number <= _proposal.endBlock) return ProposalState.Active;
    else if (
      (_whitelisted && _proposal.againstVotes > _proposal.quorumVotes)
        || (!_whitelisted && _proposal.forVotes <= _proposal.againstVotes)
        || (!_whitelisted && _proposal.forVotes < _proposal.quorumVotes)
    ) return ProposalState.Defeated;
    else if (_proposal.eta == 0) return ProposalState.Succeeded;
    else if (_proposal.executed) return ProposalState.Executed;
    else if (block.timestamp >= (_proposal.eta + GRACE_PERIOD)) return ProposalState.Expired;
    _state = ProposalState.Queued;
  }
```

According to the state function, the succeeded proposal does not have an expiration time before it enters the queue, so the check above seems unnecessary.

## Recommendations

Invalid expiration time check, can be deleted

# [L-03] UniswapV3Oracle obtains the price at a single tick that is susceptible to manipulation

## Description

```solidity
  function _getPriceFromUniswap(uint32 _seconds, uint128 _amount) internal view virtual returns (uint256 _price) {
    (int24 _arithmeticMeanTick,) = OracleLibrary.consult(address(POOL), _seconds);
    _price = OracleLibrary.getQuoteAtTick(_arithmeticMeanTick, _amount, BASE_TOKEN, QUOTE_TOKEN);
  }
```

`getQuoteAtTick` obtains the price at a single tick and `_seconds` is fixed, which leads to malicious users can manipulate the pool's temporary price in advance, and use the manipulated price to arbitrage after `_seconds`.

## Recommendations

Using TWAP prices, although it will also be manipulated, but compared to a single tick, the manipulation cost is higher.

# [L-04] The failure of the user to repay after the pause of the protocol may lead to liquidation

## Description

The calculation of interest in the protocol depends on time weighting. When VaultController suspends operation, the interest is still being calculated, but the user cannot call repay, which leads to two problems:
1. The suspension time is too long and the accumulated interest leads to the user's bankruptcy
2. There is no cooling period after the end of the suspension, and the liquidator frontrun to liquidate users, and the users have no time to repay

## Recommendations

Mark the pause time and stop calculating interest

# [L-05] VaultController _getVaultBorrowingPower function possible exhaustes gas

## Description

```solidity
  function _getVaultBorrowingPower(IVault _vault) private returns (uint192 _borrowPower) {
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

When calculating `_getVaultBorrowingPower` in VaultController, all enabledTokens will be counted, and enabledTokens can only be increased but not reduced.    
As the market matures, the number of enabledTokens will increase. The gas consumed of `_getVaultBorrowingPower` increases and may run out of gas.

## Recommendations

Add functions that allow to remove enabledTokens, or shards to calculate `_getVaultBorrowingPower`

# [L-06] The decimal of the oracle price may not be 18, causing vault to miscalculate

## Description

```solidity
  // the raw price is simply the oracle price of the token
  uint192 _rawPrice = _safeu192(_collateral.oracle.currentValue());
  emit BorrowPriceInfo(_rawPrice, _getPriceWithDecimals(_rawPrice, _collateral.decimals));
  if (_rawPrice == 0) continue;
  // the token value is equal to the price * balance * tokenLTV
  uint192 _tokenValue = _safeu192(
    _truncate(_truncate(_balance * _collateral.ltv * _getPriceWithDecimals(_rawPrice, _collateral.decimals)))
  );
  // increase the ltv of the vault by the token value
  _borrowPower += _tokenValue;
```

The conditions for the liquidation of the vault is: `_getVaultBorrowingPower(_vault) > _vaultLiability(_id)`, where _vaultLiability returns a number of 36 decimal, for the calculation of _getVaultBorrowingPower, if the oracle price decimal is not 18, it will be incorrectly calculated.

## Recommendations

Scaling raw_price to 18 precision

# [L-07] If sUSD is depegged, The vault can be arbitraged

## Description

```solidity
  if (_isUSDA) {
    // now send usda to the target, equal to the amount they are owed
    usda.vaultControllerMint(_target, _amount);
  } else {
    // send sUSD to the target from reserve instead of mint
    usda.vaultControllerTransfer(_target, _amount);
  }
```

`borrow` supports lending USDA or sUSD and treats them both as $1, but the problem is that if the price of sUSD is greater than $1, the arbitrageurs can almost drain the vaults, take all the sUSD, and then repay USDA and withdraw the staking sUSD:
1. User deposit 100 sUSD, mint 100 USDA and borrow 50 sUSD, debt 50 USDA
2. User sell 50 sUSD ($1.1), buy 55 USDA ($1)
3. User repay 50 USDA, arbitrage 5 USDA
4. User repay 100 USDA and redeem 100 sUSD

## Recommendations

The borrow sUSD quantity should be calculated using the oracle price