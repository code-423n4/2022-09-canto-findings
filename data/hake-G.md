


### Gas Optimizations
|        | Finding                                                                                         |  Instances  |
|:-------|:------------------------------------------------------------------------------------------------|:-----------:|
| [G-01] | `array.length` should be cached in `for` loop                                                   |      2      |
| [G-02] | `for` loop increments should be `unchecked{}` if overflow is not possible                       |      3      |
| [G-03] | Unnecessary init to 0                                                                           |      2      |
| [G-04] | Use custom errors rather than `revert()`/`require()` strings to save gas                        |      2      |
| [G-05] | `require()`/`revert()` checks used multiples times should be turned into a function or modifier |      2      |
### Gas overview per contract
| Contract                                                                                                                                        |  Instances  |  Gas Ops  |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:-----------:|:---------:|
| [BaseV1-core.sol](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol)           |     10      |     5     |
| [BaseV1-periphery.sol](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol) |      1      |     1     |
## Gas Optimizations
### [G-01] `array.length` should be cached in `for` loop
Caching the length array would save gas on each iteration by not having to read from memory or storage multiple times.
Example:
```solidity
uint length = array.length;
for (uint i; i < length;){
		uncheck { ++i }
}
```
*2 instances of this issue have been found:*
###### [G-01] [BaseV1-core.sol#L229-L230](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L229-L230)
```solidity

        for (uint i = 0; i < _reserves0.length; ++i) {

```
###### [G-01b] [BaseV1-core.sol#L264-L265](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L264-L265)
```solidity

        for (uint i = 0; i < _totalSupplyAvg.length; ++i) {

```
### [G-02] `for` loop increments should be `unchecked{}` if overflow is not possible
From Solidity `0.8.0` onwards using the `unchecked` keyword saves 30 to 40 gas per [loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked).
Example:
```solidity
uint length = array.length;
for (uint i; i < length;){
		uncheck { ++i }
}
```
*3 instances of this issue have been found:*
###### [G-02] [BaseV1-core.sol#L229-L230](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L229-L230)
```solidity

        for (uint i = 0; i < _reserves0.length; ++i) {

```
###### [G-02b] [BaseV1-periphery.sol#L581-L582](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L581-L582)
```solidity

        for(uint i; i < 8; ++i) {

```
###### [G-02c] [BaseV1-core.sol#L264-L265](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L264-L265)
```solidity

        for (uint i = 0; i < _totalSupplyAvg.length; ++i) {

```
### [G-03] Unnecessary init to 0
These values are set by default.
*2 instances of this issue have been found:*
###### [G-03] [BaseV1-core.sol#L229-L230](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L229-L230)
```solidity

        for (uint i = 0; i < _reserves0.length; ++i) {

```
###### [G-03b] [BaseV1-core.sol#L264-L265](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L264-L265)
```solidity

        for (uint i = 0; i < _totalSupplyAvg.length; ++i) {

```
### [G-04] Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors save ~50 gas each time they're hit by avoiding having to allocate and store the revert string.
Only one instance was given but the issue can be attributed to all `require()`/`revert()` functions that do not implement custom errors in the contract.
*2 instances of this issue have been found:*
###### [G-04] [BaseV1-core.sol#L275-L276](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L275-L276)
```solidity

        require(lastIndex >= points * window, "PAIR::NOT READY FOR PRICING");

```
###### [G-04b] [BaseV1-core.sol#L242-L243](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L242-L243)
```solidity

        require(lastIndex >= points * window, "PAIR::NOT READY FOR PRICING");

```
### [G-05] `require()`/`revert()` checks used multiples times should be turned into a function or modifier
Doing so increases code readability decreases number of instructions for the compiler.
*2 instances of this issue have been found:*
###### [G-05] [BaseV1-core.sol#L275-L276](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L275-L276)
```solidity

        require(lastIndex >= points * window, "PAIR::NOT READY FOR PRICING");

```
###### [G-05b] [BaseV1-core.sol#L242-L243](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L242-L243)
```solidity

        require(lastIndex >= points * window, "PAIR::NOT READY FOR PRICING");

```
