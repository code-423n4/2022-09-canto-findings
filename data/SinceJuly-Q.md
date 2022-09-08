## UNUSED INTERNAL FUNCTION
- Position: https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L600-L611
- Description: The function `_returnStableBooleans` is internal and not used in the contract, it is recommended to remove the function.

## HARDCODED PRICES FOR STABLECOINS
- Position:  https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L501-L508
- Description: Hardcoding price of cUSDT and cUSDC as 1 may open some arbitrage opportunities when real price for each token is a little bit different. Also, in case of UST-style collapse, the protocol will not be able to liquidate bad loans.