# QA Report for Canto Dex Oracle contest

## Overview
During the audit, 5 low and 7 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | [Check zero denominator](#l-1-check-zero-denominator) | Low | 2
L-2 | [Missing check for input variables](#l-2-missing-check-for-input-variables) | Low | 2
L-3 | [Large number of observations may cause out-of-gas error](#l-3-large-number-of-observations-may-cause-out-of-gas-error) | Low | 2
L-4 | [Incorrect comment](#l-4-incorrect-comment) | Low | 1
L-5 | [Misleading comment](#l-5-misleading-comment) | Low | 1
NC-1 | [Order of Functions](#nc-1-order-of-functions) | Non-Critical | 5
NC-2 | [Maximum line length exceeded](#nc-2-maximum-line-length-exceeded) | Non-Critical | 2+
NC-3 | [Constants may be used](#nc-3-constants-may-be-used) | Non-Critical | 18
NC-4 | [Inconsistent comment spacing and location](#nc-4-inconsistent-comment-spacing-and-location) | Non-Critical | 1
NC-5 | [Loop parameter may be changed for clarity](#nc-5-loop-parameter-may-be-changed-for-clarity) | Non-Critical | 2
NC-6 | [Functions without comments](#nc-6-functions-without-comments) | Non-Critical | 4
NC-7 | [Require statement may be placed before allocating memory for arrays](#nc-7-require-statement-may-be-placed-before-allocating-memory-for-arrays) | Non-Critical | 2

## Low Risk Findings (5)
### L-1. Check zero denominator
##### Description
If the input parameter is equal to zero, this will cause the function call failure on division.

##### Instances
- [```return (reserveAverageCumulative0 / granularity, reserveAverageCumulative1 / granularity);```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L234)
- [```return (totalSupplyCumulativeAvg / granularity);```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L268)

##### Recommendation
Add the check to prevent function call failure.

#
### L-2. Missing check for input variables
##### Description 
If input variable ```points```== 0, function will return empty array.  
More critical, if input variable ```window```== 0, function will return array with default values, which may lead to further incorrect calculations.

##### Instances
- [```function sampleReserves(uint points, uint window)```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L237)
- [```function sampleSupply(uint points, uint window)```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L271)

##### Recommendation
Add require statement or custom error - ```points!= 0 && window!= 0```.

#
### L-3. Large number of observations may cause out-of-gas error
##### Description
[Loops](https://docs.soliditylang.org/en/develop/security-considerations.html#gas-limit-and-loops) that do not have a fixed number of iterations, for example, loops that depend on storage values, have to be used carefully: Due to the block gas limit, transactions can only consume a certain amount of gas. Either explicitly or just due to normal operation, the number of iterations in a loop can grow beyond the block gas limit, which can cause the complete contract to be stalled at a certain point. 

##### Instances
- [```for(; i < lastIndex; i+=window) {```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L248) (function sampleReserves)
- [```for(; i < lastIndex; i+=window) {```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L281) (function sampleSupply)

##### Recommendation
Restrict the maximum number of sample observations (```points```).

#
### L-4. Incorrect comment
##### Instances
[// note in terms of note will always be 1 ](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L498)

##### Recommendation
Probably the comment should be like this "price in terms of note will always be 1 ".

#
### L-5. Misleading comment
##### Description
The comment is misleading, and there is an extra comma and an empty comment line.

##### Instances
[Link:](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L229-L232)
```
        for (uint i = 0; i < _reserves0.length; ++i) {
            reserveAverageCumulative0 += _reserves0[i]; //normalize the reserves for TWAP LP Oracle pricing, 
            reserveAverageCumulative1 += _reserves1[i]; //
        }
```

##### Recommendation
Change or delete comment.

## Non-Critical Risk Findings (7)
### NC-1. Order of Functions
##### Description
Some internal functions are between public, some external functions are between public, and some public functions are between external.

##### Instances
- [BaseV1-core.sol: 137](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L137)
- [BaseV1-core.sol: 224](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L224)
- [BaseV1-core.sol: 260](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L260)
- [BaseV1-core.sol: 237](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L237)
- [BaseV1-core.sol: 271](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L271)

##### Recommendation
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private

#
### NC-2. Maximum line length exceeded
##### Description
Some lines of code are too long.

##### Instances
- [```observations.push(Observation(blockTimestamp, reserve0CumulativeLast, reserve1CumulativeLast, totalSupplyCumulativeLast));```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L149)
- [```_totalSupply[index] = (observations[nextIndex].totalSupplyCumulative - observations[i].totalSupplyCumulative) / timeElapsed;```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L284)
- and more, if you take into account the beginning indent and / or comments.

##### Recommendation
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length), maximum suggested line length is 120 characters.  
Make the lines shorter.

#
### NC-3. Constants may be used
##### Description
Constants may be used instead of  literal values.

##### Instances
- [```uint[] memory supply = pair.sampleSupply(8, 1);```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L550)
- [```prices = pair.sample(token1, decimals, 8, 1);```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L561)
- [```(unitReserves, assetReserves) = pair.sampleReserves(8, 1);```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L562)
- [```prices = pair.sample(token0, decimals, 8, 1);```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L565)
- [```(assetReserves, unitReserves) = pair.sampleReserves(8, 1);```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L566)
- [```prices = pair.sample(token1, decimals, 8, 1);```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L571)
- [```(unitReserves, assetReserves) = pair.sampleReserves(8, 1);```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L572)
- [```prices = pair.sample(token0, decimals, 8, 1);```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L575)
- [```(assetReserves, unitReserves) = pair.sampleReserves(8, 1);```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L576)
- [```for(uint i; i < 8; ++i) {```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L581)
- [```return 1e18; // Stable coins supported by the lending market are instantiated by governance and their price will always be 1 note```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L499)
- [```return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L503)
- [```return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L507)
- [```return getPriceCanto(underlying) * getPriceNote(address(wcanto), false) / 1e18;```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L520)
- [```LpPricesCumulative += (token0TVL + token1TVL) * 1e18 / supply[i];```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L584)
- [```return LpPrice * getPriceNote(address(wcanto), false) / 1e18; // return the price in terms of Note```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L592)
- [```return price * 1e18 / decimals; //return the scaled price```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L533)
- [```return price * 1e18 / decimals; // divide by decimals now to maintain precision```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L545)

##### Recommendation
Define constant variables for repeated values (8 and 1e18).

#
### NC-4. Inconsistent comment spacing and location
##### Description
Some comments are above the line of code and some next to it.
Some comments are indented between *// and the comment text*, some are not.

##### Instances
- [Link:](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L489-L490)
```
{ //manual scope to pop symbol off of stack
 string memory symbol = ctoken.symbol();
```
- [```underlying = address(ICErc20(address(ctoken)).underlying()); // We are getting the price for a CErc20 lending market```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L495)
- [Link:](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L497-L499)
```
//set price statically to 1 when the Comptroller is retrieving Price
if (compareStrings(symbol, "cNOTE")) { // note in terms of note will always be 1 
    return 1e18; // Stable coins supported by the lending market are instantiated by governance and their price will always be 1 note
```
- [```return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L503)
- all other functions...

##### Recommendation
Use consistent comment spacing and location.

#
### NC-5. Loop parameter may be changed for clarity
##### Description
In loop are used ```_reserves0.length```. It is equal to input variable ```granularity```. It can be clearer and more consistent if you use an input variable in the loop.

##### Instances
1. [Link:](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L224-L229)
```
    function reserves(uint granularity) external view returns(uint, uint) {
        (uint[] memory _reserves0, uint[] memory _reserves1)= sampleReserves(granularity, 1);
        uint reserveAverageCumulative0;
        uint reserveAverageCumulative1;

        for (uint i = 0; i < _reserves0.length; ++i) {
```
2. [Link:](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L260-L264)
```
    function totalSupplyAvg(uint granularity) external view returns(uint) {
        uint[] memory _totalSupplyAvg = sampleSupply(granularity, 1);
        uint totalSupplyCumulativeAvg;

        for (uint i = 0; i < _totalSupplyAvg.length; ++i) {
```
##### Recommendation
1. Change to 
```
    function reserves(uint granularity) external view returns(uint, uint) {
        (uint[] memory _reserves0, uint[] memory _reserves1)= sampleReserves(granularity, 1);
        uint reserveAverageCumulative0;
        uint reserveAverageCumulative1;
        
        //HERE
        for (uint i = 0; i < granularity; ++i) {
```
2. Change to 
```
    function totalSupplyAvg(uint granularity) external view returns(uint) {
        uint[] memory _totalSupplyAvg = sampleSupply(granularity, 1);
        uint totalSupplyCumulativeAvg;

        //HERE
        for (uint i = 0; i < granularity; ++i) {
```
#
### NC-6. Functions without comments
##### Description
Some functions do not have comments describing them.

##### Instances
- [```function reserves(uint granularity)```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L224)
- [```function sampleReserves(uint points, uint window)```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L237)
- [```function totalSupplyAvg(uint granularity)```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L260)
- [```function sampleSupply(uint points, uint window)```](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L271)

##### Recommendation
Add comments.

#
### NC-7. Require statement may be placed before allocating memory for arrays
##### Instances
1. [Link:](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L238-L242)
```
        uint[] memory _reserves0 = new uint[](points);
        uint[] memory _reserves1 = new uint[](points);
        
        uint lastIndex = observations.length-1;
        require(lastIndex >= points * window, "PAIR::NOT READY FOR PRICING");
```
2. [Link:](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L272-L275)
```
        uint[] memory _totalSupply = new uint[](points);
        
        uint lastIndex = observations.length-1;
        require(lastIndex >= points * window, "PAIR::NOT READY FOR PRICING");
```

##### Recommendation
1. Change to 
```
        uint lastIndex = observations.length-1;
        require(lastIndex >= points * window, "PAIR::NOT READY FOR PRICING");

        uint[] memory _reserves0 = new uint[](points);
        uint[] memory _reserves1 = new uint[](points);
```
2. Change to 
```
        uint lastIndex = observations.length-1;
        require(lastIndex >= points * window, "PAIR::NOT READY FOR PRICING");
        
        uint[] memory _totalSupply = new uint[](points);
```