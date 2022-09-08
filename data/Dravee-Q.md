**Overview**
Risk Rating | Number of issues
--- | ---
Low Risk | 1
Non-Critical Risk | 3

**Table of Contents**

- [1. Low Risk Issues](#1-low-risk-issues)
  - [1.1. `granularity` can be 0 and raise unexpected errors](#11-granularity-can-be-0-and-raise-unexpected-errors)
- [2. Non-Critical Issues](#2-non-critical-issues)
  - [2.1. Remove code that serve no purpose](#21-remove-code-that-serve-no-purpose)
  - [2.2. Avoid copy-pasting code logic](#22-avoid-copy-pasting-code-logic)
  - [2.3. Change uint to uint256](#23-change-uint-to-uint256)

# 1. Low Risk Issues

## 1.1. `granularity` can be 0 and raise unexpected errors

It's possible for an non-savvy enough user to input the default value of `0` for `granularity`, which would raise unexpected errors (initialization of an array of size 0 / divide by 0 error). Consider either adding a `require` statement to output a friendly revert string, or agreeing on a default `granularity` (which should be documented):

- [function reserves](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L224)

```solidity
File: BaseV1-core.sol
224:     function reserves(uint granularity) external view returns(uint, uint) {//@audit-issue should implement a default granularity as 0 would throw an error
225:         (uint[] memory _reserves0, uint[] memory _reserves1)= sampleReserves(granularity, 1);
...
234:         return (reserveAverageCumulative0 / granularity, reserveAverageCumulative1 / granularity); //@audit-issue : granularity can be 0 but this shouldn't be reached in this case (L238 will raise an error before here)
...
237:     function sampleReserves(uint points, uint window) public view returns (uint[] memory, uint[] memory) {
238:         uint[] memory _reserves0 = new uint[](points); //@audit-issue initializing an array of size 0
```

- [function totalSupplyAvg](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L260)

```solidity
File: BaseV1-core.sol
260:     function totalSupplyAvg(uint granularity) external view returns(uint) {//@audit-issue should implement a default granularity as 0 would throw an error
261:         uint[] memory _totalSupplyAvg = sampleSupply(granularity, 1);
...
268:         return (totalSupplyCumulativeAvg / granularity);//@audit-issue : granularity can be 0 but this shouldn't be reached in this case (L272 will raise an error before here)
...
271:     function sampleSupply(uint points, uint window) public view returns (uint[] memory) {
272:         uint[] memory _totalSupply = new uint[](points); //@audit-issue initializing an array of size 0
```

# 2. Non-Critical Issues

## 2.1. Remove code that serve no purpose

[Here](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L492), assigning a value to `underlying` doesn't add any functionality, as it's a memory variable that isn't returned and a return statement is right below it. Consider removing this unnecessary line of code to avoid confusion:

```solidity
File: BaseV1-periphery.sol
487:     function getUnderlyingPrice(CToken ctoken) external override view returns(uint) {
488:          address underlying;
489:         { //manual scope to pop symbol off of stack
490:         string memory symbol = ctoken.symbol();
491:         if (compareStrings(symbol, "cCANTO")) {
492:             underlying = address(wcanto); //@audit-issue this code serves no purpose
493:             return getPriceNote(address(wcanto), false);
```

## 2.2. Avoid copy-pasting code logic

[Those 2 conditional statements](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L501-L508) do exactly the same thing, therefore they should be grouped together:

```diff
File: BaseV1-periphery.sol
- 501:         else if (compareStrings(symbol, "cUSDT") && (msg.sender == Comptroller )) { //@audit-issue avoid copy-pasting
+ 501:         else if ((compareStrings(symbol, "cUSDT") || compareStrings(symbol, "cUSDC")) && (msg.sender == Comptroller )) {
502:             uint decimals = erc20(underlying).decimals();
503:             return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller
504:         } 
- 505:         else if (compareStrings(symbol, "cUSDC") && (msg.sender == Comptroller)) { //@audit-issue avoid copy-pasting
- 506:             uint decimals = erc20(underlying).decimals();
- 507:             return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller
- 508:         }
```

## 2.3. Change uint to uint256

In the whole scope of the contest, unsigned integers are **all** declared as `uint`. To favor explicitness, consider changing all instances of `uint` to `uint256`:
