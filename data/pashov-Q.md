**Misleading variable name**

The local variable `decimals` in both `getPriceCanto()` and `getPriceNote()` is not really decimals - it is 10 to the power of the token decimals. It is passed to the `IBaseV1Pair::quote()` function as the `granularity` argument. Rename variable from `decimals` to `granularity

And again, `decimals` in `getPriceLP()` is not really decimals, but it is 10 to the power of a token’s decimals. It is passed to the `IBaseV1Pair::sample()` function as the `amountIn` argument. Rename variable from `decimals` to `amount` or `amountIn`.

**Inconsistent variable naming**

In `BaseV1-core.sol` for the `reserves()` function we have a `granularity` parameter. The problem is the same parameter is passed as the argument named `points` for `sampleReserves`. It is the same situation with `totalSupplyAvg()`'s `granularity` and `sampleSupply`'s `points` parameter. Choose only `granularity` or only `points` and stick to this naming to simplify the code for both developers and auditors.

**Division before multiplication**

In `getPriceLP()` we have 

```
uint token0TVL = assetReserves[i] * (prices[i] / decimals);
```

Where the division comes before the multiplication because of the braces. This is a bad practice in Solidity, because there is a loss of precision this way, and also if `prices[i]` is smaller than `decimals` then the division will return 0 and the value of `token0TVL` will be zero, whatever the `assetReserves[i]` are. So instead, first do the multiplication and then the division.