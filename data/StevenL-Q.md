First QA issue - require() should be used instead of assert()

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as require()/revert() do. assert() should be avoided even past solidity version 0.8.0 as its documentation states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol
85: assert(msg.sender == address(wcanto));
236: assert(amountAOptimal <= amountADesired);
282: assert(wcanto.transfer(pair, amountCANTO));
428: assert(wcanto.transfer(pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]));