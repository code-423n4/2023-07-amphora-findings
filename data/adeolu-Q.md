# no need for "<= 0" check on uint256 value

**Explanation**
since value is uint256, its lowest value by default is 0, so the check should be "== 0" not "<= 0"

**Proof Of Concept**
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L14
```
  constructor(
    address _account,
    uint256 _initialSupply
  ) ERC20('Amphora Protocol', 'AMPH') ERC20Permit('Amphora Protocol') {
    if (_account == address(0)) revert AmphoraProtocolToken_InvalidAddress();
    if (_initialSupply <= 0) revert AmphoraProtocolToken_InvalidSupply();

    _mint(_account, _initialSupply);
  }
```

**Recommended Mitigation**

change <= to ==
```
  constructor(
    address _account,
    uint256 _initialSupply
  ) ERC20('Amphora Protocol', 'AMPH') ERC20Permit('Amphora Protocol') {
    if (_account == address(0)) revert AmphoraProtocolToken_InvalidAddress();
    if (_initialSupply == 0) revert AmphoraProtocolToken_InvalidSupply();

    _mint(_account, _initialSupply);
  }
```

