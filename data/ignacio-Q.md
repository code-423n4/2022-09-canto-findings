 #1 ABI.ENCODEPACKED() SHOULD NOT BE USED WITH DYNAMIC TYPES WHEN PASSING THE RESULT TO A HASH FUNCTION SUCH AS KECCAK256() 

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L103
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L597
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L603
#2 A miner can manipulate the block timestamp which can be used to their advantage to attack a smart contract via Block Timestamp Manipulation

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L138 
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L159
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L176
Blocks have a timestamp field in the block header which is set by the miner and can be changed to whatever they want (with some restriction). In order for a miner to set a block timestamp they need to win the next block and abide by the following time constrains:

The next timestamp is after the last block timestamp
The timestamp can not be too far into the future
If the miner wins a block they can slightly change the block timestamp to their advantage.

## Impact
Dishonest  Miners can influence the value of block.timestamp to perform Maximal Extractable Value (MEV) attacks.
The use of now creates a risk that time manipulation can be performed to manipulate price oracles. Miners can modify the timestamp by up to 900 seconds , Usually to an extent of few seconds on Ethereum, or generally few percent of the block time on any EVM-compatible PoW network.

## Recommended Mitigation Steps
Use block.number instead of  block.timestamp or now to reduce the risk of
MEV attacks 

### here some reference :
https://www.bookstack.cn/read/ethereumbook-en/spilt.14.c2a6b48ca6e1e33c.md
https://ethereum.stackexchange.com/questions/108033/what-do-i-need-to-be-careful-about-when-using-block-timestamp

