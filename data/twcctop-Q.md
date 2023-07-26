https://github.com/code-423n4/2023-07-amphora/blob/5d1cea9410db5448760c834f001af04a72edf3e0/core/solidity/contracts/core/VaultController.sol#L561


```solidity
    
if (_isUSDA) {
      // now send usda to the target, equal to the amount they are owed
      usda.vaultControllerMint(_target, _amount);
    } else {
      // send sUSD to the target from reserve instead of mint
      usda.vaultControllerTransfer(_target, _amount);
    }

// emit the event
    emit BorrowUSDA(_id, address(_vault), _amount, _fee);


```

seem that whether borrow asset is USDA or sUSD,it will emit same message