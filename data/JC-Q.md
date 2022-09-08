L-01 same name local variable in the same contract

in function _update()
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L138
	      uint blockTimestamp = block.timestamp;

in function currentCumulativePrices()
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L159
	        blockTimestamp = block.timestamp;