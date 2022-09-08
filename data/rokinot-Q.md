### Low

Stables cTokens prices are hard set to 1e18, however cStables/Stables or even cStables/cStables pairs will have variant prices in terms of Note (i.e. cUSDC/cUSDT will have prices that can be different than 1e18).

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L549

## Non-critical

### Wrong comment, should be `WCANTO` instead of `WETH`

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L85

### Contracts can force deposit canto by calling selfdestruct

Although there's no vulnerability involved, this goes against what the comment says.

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L85