# State variable visibility is not set. 

Labeling the visibility explicitly makes it easier to catch incorrect assumptions about who can access the variable.

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L34 
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L536
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L64

Reference: https://swcregistry.io/docs/SWC-108

# Requirement Violation

A requirement was violated in a nested call and the call was reverted as a result.

Reference: https://swcregistry.io/docs/SWC-123

Reproduce:

Case 1:

Call TX 1:
address (empty)
gasLimit 0x2ffffff
gasPrice 0x3b9aca000
input 0x6080604052610e10600355348015610016576000...
origin 0xaffeaffeaffeaffeaffeaffeaffeaffeaffeaffe
value 0x0
blockCoinbase 0xaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa0
blockDifficulty 0x0
blockGasLimit 0x2ffffff
blockNumber 0x0
blockTime 0x0

Call TX 2:
address 0x0901d12ebe1b195e5aa8748e62bd7734ae19b51f
gasLimit 0xff000
gasPrice 0xa055ce537
input 0x82dfdce40000000000000000000000000901d12e...
origin 0xaffeaffeaffeaffeaffeaffeaffeaffeaffeaffe
value 0x0
blockCoinbase 0xaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa0
blockDifficulty 0x0
blockGasLimit 0xff0000
blockNumber 0x24797
blockTime 0x9e
decodedInput createPair(0x0901d12ebe1b195e5aa8748e62bd7...
name createPair(address,address,bool)

Case 2:

Call TX 1:
address (empty)
gasLimit 0x2ffffff
gasPrice 0x3b9aca000
input 0x6080604052610e10600355348015610016576000...
origin 0xaffeaffeaffeaffeaffeaffeaffeaffeaffeaffe
value 0x0
blockCoinbase 0xaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa0
blockDifficulty 0x0
blockGasLimit 0x2ffffff
blockNumber 0x0
blockTime 0x0

Call TX 2:
address 0x0901d12ebe1b195e5aa8748e62bd7734ae19b51f
gasLimit 0xff000
gasPrice 0x3140bafa4
input 0x82dfdce400000000000000000000000030e5cb02...
origin 0xaffeaffeaffeaffeaffeaffeaffeaffeaffeaffe
value 0x0
blockCoinbase 0xaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa0
blockDifficulty 0x0
blockGasLimit 0xff0000
blockNumber 0x50f82
blockTime 0x1
decodedInput createPair(0x30e5cb02e97db27ec3bf267c51e9a... 
name createPair(address,address,bool)

# Missing events

Events are important and should be emitted for tracking this off-chain for all important functions.
 
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L101

Mitigation
Add an event to this important function. This can also be marked as indexed event for better off-chain  tracking


# Missing zero-address validations


While the codebase does a great job of input validation  for parameters of all kinds and especially addresses, there are a few  places where zero-address validations are missing. None of them are  catastrophic, will result in obvious reverts and can be reset given the  permissioned/controlled interactions with the contracts.
Nevertheless, it is helpful to add zero-address  validations to be consistent and ensure high availability of the  protocol with resistance to accidental misconfigurations.

POC

https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L555L558
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L83L84



