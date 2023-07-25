## VaultController
1) checkVault and _getVaultBorrowingPower could be only view
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L866
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L462
2) should uint256() incapsulate some uint192 variable,just to make sure that the calculation will go through
--should be uint256(interest.factor) in https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L453
--should be uint256(interest.factor) in https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L468
--uint256(_baseAmount) in https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/VaultController.sol#L603
## Governor Charlie
1) Should set initialProposalId variable to 0 in the constructor or directly hardcoded
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L44
2) should remove = in the if condition at line 471,this will make unfair the proposal,resulting in a win for one part even if there're the same amount
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L471
3) could be implemented the version of the contract to better follow the standard quality rules,by changing the DOMAIN_TYPEHASH to:
``bytes32 public constant DOMAIN_TYPEHASH =keccak256('EIP712Domain(string name,keccak256(bytes(VERSION)),uint256 chainId,address verifyingContract)');``
then add a version variable at the top,like this:
``string public constant NAME = '1.0';``
## AmphoraProtocolToken
1) should be != 0 and not <= 0
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/AmphoraProtocolToken.sol#L14
## ThreeLines0_100
1) !0<S1 and !0<S2 are useless checks,should remove them
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/ThreeLines0_100.sol#L34-L35
2) comments are wrote wrong,in the end the xValue will be ALWAYS == 1e18 if not,the function have already returned something and wont arrive that far,for 2 reasons(rules) setted on the constructor:
--S1 needs to be higher or equal to S2
--S2 needs to be higher or equal to 1e18
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/ThreeLines0_100.sol#L66
3) Should return R2 instantly after the first if statemenet((_xValue > 1e18))) at line 51 for the same reasons quoted above
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/ThreeLines0_100.sol#L67
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/ThreeLines0_100.sol#L51