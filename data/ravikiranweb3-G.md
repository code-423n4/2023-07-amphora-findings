1) In GovernorCharlie contract, while processing the proposal queue, eta is checked for each target in the array, which is not necessary.

This check can be moved from _queueTransaction to queue function and can be checked once before the loop of targets. This will save some gas.

