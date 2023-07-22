# Not emitting event for important state changes

## Details
- Locations: 
- -  ___ChainlinkOracleRelay.sol___ - "setStalePriceDelay()"
- - ___CbEthEthOracle.sol___ - "_updateVirtualPrice()"
- - ___OracleRelay.sol___ - "setUnderlying(address)"
- - ___CTokenOracle___ - "changeAnchoredView(address)"
- - ___EthSafeStableCurveOracle___ - "_updateVirtualPrice()"

## Impact
 The system does not record important state changes.

## Tools used
 Manual review

## Recomendations
 For set/change... function emit events with old and new value.