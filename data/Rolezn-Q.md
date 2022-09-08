## (1) Constants Should Be Defined Rather Than Using Magic Numbers

Severity: Non-Critical

## Proof Of Concept

    return 1e18; // Stable coins supported by the lending market are instantiated by governance and their price will always be 1 note
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L499


## (2) Use a more recent version of Solidity

Severity: Non-Critical

Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

## Proof Of Concept

	Found old version 0.8.11 of Solidity in BaseV1-core.sol
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L2

	Found old version 0.8.11 of Solidity in BaseV1-periphery.sol
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L3



## Recommended Mitigation Steps

Consider updating to a more recent solidity version.


## (3) Non-usage of specific imports

Severity: Non-Critical

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

## Proof Of Concept

	import "../deps/CToken.sol";
	import "../deps/PriceOracle.sol";
	import "./BaseV1-libs.sol";
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L5-L7

	import "./BaseV1-libs.sol";
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L4

## Recommended Mitigation Steps

Use specific imports syntax per solidity docs recommendation.