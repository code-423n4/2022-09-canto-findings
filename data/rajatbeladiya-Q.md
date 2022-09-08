1). add zero address validation

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L525

- getPriceCanto() will fail for zero address as it is fetching decimals() on it.

2). sampleReserves() returns _reserves0 and _reserves1 as (0,0) when window is 0

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L237-L258

- add check for window > 0
