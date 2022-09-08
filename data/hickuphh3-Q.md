# Low Severity Findings

## L01: Misleading variable names `token0TVL` and `token1TVL`

### Line References

[https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L582-L583](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L582-L583)

### Description

The variables `token0TVL` and `token1TVL` are misleading because `assetReserves` and `unitReserves` could be either `token0` or `token1` amounts, depending on whether `token0` is the NOTE or CANTO token.

```solidity
uint token0TVL = assetReserves[i] * (prices[i] / decimals);
uint token1TVL = unitReserves[i]; // price of the unit asset is always 1
```

### Recommended Mitigation Steps

Consider better names indicating that they are referring to the TVL of the asset and unit tokens. Something like `assetTokenTVL` and `unitTokenTVL`.

# Non-Critical Findings

## NC01: Geometric mean / More established oracle services should be used

### Line References

[https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L558-L586](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L558-L586)

### Description

The LP token’s value is determined by a 30-minutes arithmetic mean TWAP (am-TWAP) of the past 8 observations.

Hayden from Uniswap tweeted that [*“gmTWAP (geometric mean TWAP) is equally expensive to manipulate in both directions under geometric brownish motion, whereas amTWAP overweights upwards movements a huge amount relative to downward movements (making it far easier to manipulate if you know what you’re doing)”](*[https://twitter.com/haydenzadams/status/1455238967427796993](https://twitter.com/haydenzadams/status/1455238967427796993)). 

This is supported by a [research paper on TWAP Oracle attacks]([https://eprint.iacr.org/2022/445.pdf](https://eprint.iacr.org/2022/445.pdf)). I reproduced a portion of the “results” section below.

> We compare the three different TWAP manipulation attacks
we have seen so far. We again use the example where we want
to double the price of an asset which uses a 30-minute TWAP
that uses a pair with \$2,000,000 of total liquidity reserves.
The cost of the MMEV single-block attack with 1.5% hash
rate is \$160,000. Based on equation 1, the cost of the singleblock attack is C1(135 · 1) = \$9,750,000 and the cost of the
multi-block attack is 135 · C1(1) = \$16,200,000.
All attacks ignore the rather small AMM trading fee of
around $35,000.
> 

It goes on to present the geometric mean as a solution to the oracle manipulation.

However, calculation of the geometric mean for the current 8 observations is non-trivial because the multiplication of the values will likely lead to overflow. A naiive method would be to square root each observation thrice, then multiply them together. This however might lead to potential rounding errors, with significant gas usage as well.

A more viable solution would be to push for established oracle companies like Chainlink, Band Protocol and Flux to deploy on Canto for usage. 

## NC02: Redundancies

### Line References

[https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L492](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L492)

[https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L558-L586](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L558-L586)

### Description

- Redundant `underlying` assignment

```solidity
if (compareStrings(symbol, "cCANTO")) {
  underlying = address(wcanto); // redundant
  return getPriceNote(address(wcanto), false);
```

The assignment of `wcanto` to the `underlying` variable is redundant since it is not used in this branch.

- Redundant `else` case

```solidity
else {
  if (isStable[underlying]) {
    return getPriceNote(underlying, true); // value has already been scaled
  }

  return getPriceCanto(underlying) * getPriceNote(address(wcanto), false) / 1e18;
}
```

The `else` case here is redundant and thus can be dropped because the `if` case returns early.

## NC03: Have `isPriceOracle` indicator

### Line References

[https://github.com/Canto-Network/clm/blob/main/src/PriceOracle.sol#L7-L8](https://github.com/Canto-Network/clm/blob/main/src/PriceOracle.sol#L7-L8)

### Description

There is an `isPriceOracle` constant that is present in the `PriceOracle` contract. Consider having it included in the periphery contract as well, even though the Comptroller doesn’t check it.