In [VaultController.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L11-L12), there are two unused imports:

1. `import {IBooster} from '@interfaces/utils/IBooster.sol'`
2. `import {IBaseRewardPool} from '@interfaces/utils/IBaseRewardPool.sol'`

These imports are present in the code but are not utilized anywhere within the file. It's considered good practice to remove such unused imports to keep the codebase clean and reduce unnecessary dependencies. Removing them will not impact the functionality of the code in [VaultController.sol](https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L11-L12) since they are not referenced or used in any of the logic within the file.