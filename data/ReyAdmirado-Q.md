## 1. constants should be defined rather than using magic numbers 

Even assembly can benefit from using readable constants instead of hex/numeric literals

- [BaseV1-periphery.sol#L499](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L499)
- [BaseV1-periphery.sol#L503](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L503)
- [BaseV1-periphery.sol#L507](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L507)
- [BaseV1-periphery.sol#L520](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L520)
- [BaseV1-periphery.sol#L550](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L550)
- [BaseV1-periphery.sol#L560-576](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L560)
- [BaseV1-periphery.sol#L581](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L581)
- [BaseV1-periphery.sol#L584](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L584)
- [BaseV1-periphery.sol#L586](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L586)
- [BaseV1-periphery.sol#L592](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L592)
- [BaseV1-periphery.sol#L532-533](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L532)
- [BaseV1-periphery.sol#L544-545](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L544)


## 2. inconsistent spacing in comments

Some lines use // x and some use //x. The instances below point out the usages that don’t follow the majority, within each file

- [BaseV1-periphery.sol#L486](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L486)
- [BaseV1-periphery.sol#L489](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L489)
- [BaseV1-periphery.sol#L497](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L497)
- [BaseV1-periphery.sol#L503](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L503)
- [BaseV1-periphery.sol#L507](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L507)
- [BaseV1-periphery.sol#L559](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L559)
- [BaseV1-periphery.sol#L524](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L524)
- [BaseV1-periphery.sol#L533](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L533)

- [BaseV1-core.sol#L143](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L143)
- [BaseV1-core.sol#L230](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L230)


## 3. Scoped contracts are missing proper NatSpec comments

The scoped contracts are missing proper NatSpec comments such as @notice @dev @param on many places. It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI)


## 4. Outdated compiler version

Using old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs


## 5. empty comment

- [BaseV1-core.sol#L231](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L231)


## 6. missing space before and after the operator

- [BaseV1-core.sol#L241](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L241)
- [BaseV1-core.sol#L248](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L248)
- [BaseV1-core.sol#L274](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L274)
- [BaseV1-core.sol#L281](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L281)

