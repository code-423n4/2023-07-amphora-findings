
Code Quality Report
===================

## [Low-01] Assert instead require to validate user inputs
From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix. With assert the user pays the gas and with require it does not. The ETH network gas is not cheap and users can see it as a scam.

- Deploy.s.solL#90
- AMPHClaimer.t.solL#67
- AMPHClaimer.t.solL#74
- AMPHClaimer.t.solL#78
- AMPHClaimer.t.solL#86


## [Low-02] Loss of precision
Doing multiplication before the divisions leads to better precision.

- AMPHClaimer.solL#196
- VaultController.t.solL#898
- VaultController.solL#946


## [Low-03] Loss of precision by using division over possible multiplication
In cases of computing a / b < c you could improve precision by doing instead a < c * b.

- AnchoredViewRelay.t.solL#111
- ChainlinkOracleRelay.t.solL#58
- CTokenOracle.t.solL#69
- CurveLpOracle.t.solL#87
- EthSafeStableCurveLpOracle.t.solL#150


## [Low-04] open TODOs
You have open TODOs:

- RegisterToken.s.solL#13
- AMPHClaimer.t.solL#133
- VaultControllerBorrowerHandler.solL#80
- VaultControllerOwnerHandler.solL#82
- VaultControllerOwnerHandler.solL#245


## [Low-05] Missing require message
The following requires are with empty messages. 
 This is very important to add a message for any require so that the user has enough information to understand the reason of failure.

- UFragments.solL#324
- UFragments.solL#333


## [Low-06] Change owner with two steps verification process
Consider having two steps verification to change owner to avoid human errors. The following contracts use direct transfer.

- CurveMaster.t.sol

