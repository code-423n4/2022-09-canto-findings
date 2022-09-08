## Double Cast of Address
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L495

The returned type of `underlying()` is an address. Hence, there is no need to cast it into type address again. The above line code of code could be rewritten as follows:

```
underlying = ICErc20(address(ctoken)).underlying();
```
## Reserves Should Match to Balances First
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L151-L152

The above two lines of codes should be moved to above the first if condition in `_update()` so that resrve0 and reserve1 are matched to balance0 and balance1 first prior to performing further calculations and/or observations update. Doing this will be more in line with the intended purpose of `sync()`.
