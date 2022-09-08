- [Low](#low)
    - [**1. It's safer compare by address**](#1-its-safer-compare-by-address)
    - [**2. Excessive gas consumption due to wrong definition**](#2-excessive-gas-consumption-due-to-wrong-definition)
- [Non critical](#non-critical)
    - [**3. Inaccurate comment**](#3-inaccurate-comment)

# Low


## **1. It's safer compare by address**

In the `getUnderlyingPrice` method it can be seen how multiple comparisons are made by the contract symbol, (which by the way does not appear as an [external calls](https://github.com/code-423n4/2022-09-canto/blob/main/README.md#external-calls) in the readme, and it is), instead of being compared by the address of the expected token itself. Keep in mind that this value can be easily falsified and is not reliable, so the result of these comparisons cannot be trusted either.

**Affected source code:**

- [BaseV1-periphery.sol:491-496](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L491-L496)

**Recommended changes:**

```diff
+       underlying = address(ICErc20(address(ctoken)).underlying()); // We are getting the price for a CErc20 lending market
-       if (compareStrings(symbol, "cCANTO")) {
+       if (underlying == address(wcanto)) {
-           underlying = address(wcanto);
            return getPriceNote(address(wcanto), false);
+       }
-       } else {
-           underlying = address(ICErc20(address(ctoken)).underlying()); // We are getting the price for a CErc20 lending market
-      }
```

## **2. Excessive gas consumption due to wrong definition**

The `sampleReserves` and `sampleSupply` methods are prone to overgassing because the array of size `n` is created first before checking that it can be done. This can facilitate denials of service with large samples (`points`), or significantly increase the gas consumption of an user, which may cause economic losses for the sender of the transaction.

The correct thing would be to create the high-cost variables after passing the appropriate verifications.

**Proof of concept:**

- Call: `sampleReserves(10000000,1);`

    *Result of sending the transaction:*

    > out of gas - The transaction ran out of gas. Please increase the Gas Limit.

**Affected source code:**

- [BaseV1-core.sol:238-239](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L238-L239)
- [BaseV1-core.sol:272](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L272)

**Recommended changes:**

```diff
    function sampleReserves(uint points, uint window) public view returns (uint[] memory, uint[] memory) {
-       uint[] memory _reserves0 = new uint[](points);
-       uint[] memory _reserves1 = new uint[](points);
        
        uint lastIndex = observations.length-1;
        require(lastIndex >= points * window, "PAIR::NOT READY FOR PRICING");
        uint i = lastIndex - (points * window); // point from which to begin the sample
        uint nextIndex = 0;
        uint index = 0;
        uint timeElapsed;
+       uint[] memory _reserves0 = new uint[](points);
+       uint[] memory _reserves1 = new uint[](points);

        for(; i < lastIndex; i+=window) {
            nextIndex = i + window;
            timeElapsed = observations[nextIndex].timestamp - observations[i].timestamp;
            _reserves0[index] = (observations[nextIndex].reserve0Cumulative - observations[i].reserve0Cumulative) / timeElapsed;
            _reserves1[index] = (observations[nextIndex].reserve1Cumulative - observations[i].reserve1Cumulative) / timeElapsed;
            
            index = index + 1;
        }

        return (_reserves0, _reserves1);
    }
    ...
    function sampleSupply(uint points, uint window) public view returns (uint[] memory) {
-       uint[] memory _totalSupply = new uint[](points);
        
        uint lastIndex = observations.length-1;
        require(lastIndex >= points * window, "PAIR::NOT READY FOR PRICING");
        uint i = lastIndex - (points * window); // point from which to begin the sample
        uint nextIndex = 0;
        uint index = 0;
        uint timeElapsed;
+       uint[] memory _totalSupply = new uint[](points);

        for(; i < lastIndex; i+=window) {
            nextIndex = i + window;
            timeElapsed = observations[nextIndex].timestamp - observations[i].timestamp;
            _totalSupply[index] = (observations[nextIndex].totalSupplyCumulative - observations[i].totalSupplyCumulative) / timeElapsed;
            index = index + 1;
        }

        return _totalSupply;
    }
```

---

# Non critical

## **3. Inaccurate comment**

The comment says 30 minutes, this is the default value, it could be a different one defined by `setPeriodSize` function.

```javascript
timeElapsed = blockTimestamp - _point.timestamp; // compare the last observation with current timestamp, if greater than 30 minutes, record a new event
        if (timeElapsed > periodSize) {
            observations.push(Observation(blockTimestamp, reserve0CumulativeLast, reserve1CumulativeLast, totalSupplyCumulativeLast));
        }
```

**Affected source code:**

- [BaseV1-core.sol:147-150](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L147-L150)


