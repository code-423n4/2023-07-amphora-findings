# Low Risk Findings

## [L‑01] Use Ownable2Step rather than Ownable
`Ownable2Step` and `Ownable2StepUpgradeable` prevent the contract ownership from mistakenly being transferred to an address that cannot handle it (e.g. due to a typo in the address), by requiring that the recipient of the owner permissions actively accept via a contract call of its own.
```
23: contract VaultController is Pausable, IVaultController, ExponentialNoError, Ownable {
```

```
20: contract UFragments is Ownable, IERC20Metadata {
`
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol#L20

```
contract ChainlinkOracleRelay is OracleRelay, Ownable {
```
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/periphery/oracles/ChainlinkOracleRelay.sol#L11

```
contract CTokenOracle is OracleRelay, Ownable {
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CTokenOracle.sol#L10



## [L-02] Dangerous Solidity compiler pragma range that spans breaking versions
In most contracts, the pragma statements are declared as pragma solidity >=0.6.0 <0.8.0;, which are unlocked and could cause the contracts to accidentally be compiled or deployed using an outdated or buggy compiler version.
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/periphery/oracles/UniswapV3OracleRelay.sol#L2C1-L2C1

# Non-Ctitical findings
## [N‑01] Use scientific notation rather than exponentiation 
While the compiler knows to optimize away the exponentiation, it’s still better coding practice to use idioms that do not require compiler optimization, if they exist.
`uint256 public constant MAX_wUSDA_SUPPLY = 10_000_000 * (10 ** 18); // 10 M`
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/core/WUSDA.sol#L35C3-L35C78

` ) = _peekLiquidationMath(_id, _assetAddress, 2 ** 256 - 1);`

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol#L712

## assign it to a  variable for better readability
```
514 :` if (uint256(_s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) {`
```
https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol#L514


## [N-02] Use a non-legacy and more battle-tested version
Use 0.8.10

## [S-03] Generate perfect code headers every time
Description:
I recommend using header for Solidity code layout and readability

```
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

## [N-04] NatSpec comments should be increased in contracts
> all contest

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity Official documentation

in complext project such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

## [N-05] Include return parameters in NatSpec comments
> all contest

## [N-06] Assembly Codes Specific – Should Have Comments
>Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.
>This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.
>Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

```
assembly {
      _chainId := chainid()
    }
```
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/utils/UFragments.sol#L165C5-L167C6