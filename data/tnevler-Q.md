# Low Risk and Non-Critical Issues 
## Low Risk ##

### [L-01]: Division by 0 ###

**Context:**

```
return (reserveAverageCumulative0 / granularity, reserveAverageCumulative1 / granularity);
```

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L234


```
return (totalSupplyCumulativeAvg / granularity);
```

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L268

**Description:**

Input variable **granularity** can be zero. This will cause division by zero.

**Recommendation:**

Add a check at the beginning of the both functions:
```
require(granularity > 0);
```

### [L-02]: Check input variables that they are not equal to 0 ###

**Context:**

+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L237

+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L271

**Description:**

Input variable **points** and **window** can be equal to 0. Then [BaseV1-core.sampleReserves](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L237) and [BaseV1-core.sampleSupply](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L271) functions can return array with length = 0 or array with lenght = points but it will contain only zeros.

**Recommendation:**

Add a check at the beginning of the both functions:
```
require(points > 0 && window > 0);
```


## Non-Critical Issues ##

### [N-01]: Use constants instead of literal values ###
**Context:** 

+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L581 (8)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L586 (8)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L532 (8)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L584 (1e18)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L520 (1e18)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L499 (1e18)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L503 (1e18)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L507 (1e18)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L550 (8)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L561 (8)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L565 (8)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L566 (8)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L571 (8)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L575 (8)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L576 (8)
+ https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L592 (1e18)

**Description:**

Use constant variables to make the code easier to understand and maintain.

**Recommendation:**

Define constants for these literal values: 8, 1e18.


### [N-02]: Wrong order of functions ###
**Context:** 

+ [BaseV1-periphery.getUnderlyingPrice](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L487)
+ [BaseV1-core._update](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L137)
+ [BaseV1-core.reserves](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L224)
+ [BaseV1-core.totalSupplyAvg](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L260) 

**Description:**

According [official solidity documentation](https://docs.soliditylang.org/en/v0.8.6/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

**Recommendation:**

Put the functions in the correct order according to the documentation.


### [N-03]: Use input variable instead of reading array length in every loop of a for-loop ###
**Context:** 

```
for (uint i = 0; i < _reserves0.length; ++i) {
```

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L229


```
for (uint i = 0; i < _totalSupplyAvg.length; ++i) {
```

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L264

**Description:**

granularity is equal to the length of the array.

**Recommendation:**

Change [L229](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L229) and [L264](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L264) to:

```
for (uint i = 0; i < granularity; ++i) {
```


### [N-04]: Line is too long ###
**Context:** 

```
observations.push(Observation(blockTimestamp, reserve0CumulativeLast, reserve1CumulativeLast, totalSupplyCumulativeLast));
```

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L149

**Description:**

According [official solidity documentation](https://docs.soliditylang.org/en/v0.8.14/style-guide.html#maximum-line-length) line lengths are recommended to be no greater than 79 (or 99) characters. [L149](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L149) length is 122 characters.

**Recommendation:**

Reduce line length.
