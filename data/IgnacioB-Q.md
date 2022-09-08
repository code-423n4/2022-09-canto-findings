# ABI.ENCODEPACKED() SHOULD NOT BE USED WITH DYNAMIC TYPES WHEN PASSING THE RESULT TO A HASH FUNCTION SUCH AS KECCAK256() ,
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L103
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L597
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L603

