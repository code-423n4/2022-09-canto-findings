## low risks
### L01: the assert() function when false, uses up all the remaining gas and reverts all the changes made.
#### problem
assert(false) compiles to 0xfe, which is an invalid opcode, using up all remaining gas, and reverting all changes.
require(false) compiles to 0xfd which is the REVERT opcode, meaning it will refund the remaining gas. The opcode can also return a value (useful for debugging), but I don't believe that is supported in Solidity as of this moment. (2017-11-21)
#### prof
Swap/BaseV1-periphery.sol, 236, b'                assert(amountAOptimal <= amountADesired);'
Swap/BaseV1-periphery.sol, 282, b'        assert(wcanto.transfer(pair, amountCANTO));'
Swap/BaseV1-periphery.sol, 428, b'        assert(wcanto.transfer(pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]));'


### L02:_SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE
#### problem
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function
#### prof
Swap/BaseV1-core.sol, 305, b'            _mint(address(0), MINIMUM_LIQUIDITY); // permanently lock the first MINIMUM_LIQUIDITY tokens'
Swap/BaseV1-core.sol, 311, b'        _mint(to, liquidity);'


### L03: address variable should check if it is zero MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES
#### prof
Swap/BaseV1-core.sol, 557, b'        admin = admin_;'
Swap/BaseV1-core.sol, 577, b'        pendingPauser = _pauser;'
Swap/BaseV1-core.sol, 582, b'        pauser = pendingPauser;'
Swap/BaseV1-periphery.sol, 76, b'        factory = _factory;'
Swap/BaseV1-periphery.sol, 78, b'        wcanto = IWCANTO(_wcanto);'
Swap/BaseV1-periphery.sol, 79, b'        note = note_;'
Swap/BaseV1-periphery.sol, 80, b'        Comptroller = Comptroller_;'