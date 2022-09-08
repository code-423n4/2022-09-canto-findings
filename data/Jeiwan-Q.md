# [Q01] Unused assignment
## Targets:
- https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L492
## Description
Due to the early return in [the next line](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L493),
the assignment to `underlying` is not used.