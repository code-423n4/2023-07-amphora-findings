Repeated or Identical functions in the `WUSDA.sol` contract 

```solidity
function burnAll() external override returns (uint256 _usdaAmount) {
    uint256 _wusdaAmount = balanceOf(_msgSender());
    _usdaAmount = _wUSDAToUSDA(_wusdaAmount, _usdaSupply());
    _withdraw(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
  }
  function withdrawAll() external override returns (uint256 _wusdaAmount) {
    _wusdaAmount = balanceOf(_msgSender());
    uint256 _usdaAmount = _wUSDAToUSDA(_wusdaAmount, _usdaSupply());
    _withdraw(_msgSender(), _msgSender(), _usdaAmount, _wusdaAmount);
  }
```
`link to code :` core/solidity/contracts/core/WUSDA.sol