## No check for periodSize

Contract:
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L99

Issue:
In setPeriodSize function, there is no check to validate that new periodSize_>0 which can lead to allowing frequent price updation via _update function

Recommendation:

```
require(periodSize_>0, "Incorrect period size");
```

## Zero address check missing

Contract:
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L555

Issue:
In setAdmin function, zero address check is missing for admin

Recommendation:
Check for zero address missing
Same goes for skim/burn/mint function

## Non existing Pair check missing

Contract:
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L289

Issue:
removeLiquidity function along with many swap function are missing checks to see if pair actually exists. These functions are moving forward with a pair which does not exist and process user funds and even try to remove liquidity from these pairs

Recommendation:
Check whether pair actually exists using isPair function from BaseV1 Factory