## 1. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom

###  Description:
It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s ***safeTransfer/safeTransferFrom*** unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

### Instances
[BaseV1-periphery.sol:L282](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L282)
[BaseV1-periphery.sol:L300](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L300)
[BaseV1-periphery.sol:L428](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L428)
[BaseV1-periphery.sol:L467](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L467)
[BaseV1-periphery.sol:L475](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L475)



### Actual codes used:


```
src/Swap/BaseV1-periphery.sol:282:        assert(wcanto.transfer(pair, amountCANTO));
src/Swap/BaseV1-periphery.sol:300:        require(IBaseV1Pair(pair).transferFrom(msg.sender, pair, liquidity)); // send liquidity to pair

src/Swap/BaseV1-periphery.sol:428:        assert(wcanto.transfer(pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]));
src/Swap/BaseV1-periphery.sol:467:        token.call(abi.encodeWithSelector(erc20.transfer.selector, to, value));
src/Swap/BaseV1-periphery.sol:475:        tokenCon.transferFrom(from, to, value);

```
### Recommended Mitigation Steps

Consider using safeTransfer/safeTransferFrom or require() consistently.


-----

## 2. _safeMint() should be used rather than _mint() wherever possible


###  Description:

_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both open OpenZeppelin and solmate have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

### Instances
[BaseV1-core.sol:L305](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L305)
[BaseV1-core.sol:L311](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L311)


### Actual codes used:
```
src/Swap/BaseV1-core.sol:305:            _mint(address(0), MINIMUM_LIQUIDITY); // permanently lock the first MINIMUM_LIQUIDITY tokens
src/Swap/BaseV1-core.sol:311:        _mint(to, liquidity);

```
### Recommendations:

Use _safeMint() instead of _mint().

-----

## 3.`EVENT` IS MISSING INDEXED FIELDS

Each event should use three indexed fields if there are three or more fields.

### Instances:
[BaseV1-core.sol:L65](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L65)
[BaseV1-core.sol:L66](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L66)
[BaseV1-core.sol:L546](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L546)
[BaseV1-core.sol:L76](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L76)
[BaseV1-core.sol:L78](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L78)
[BaseV1-core.sol:L79](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L79)


### Actual codes used:

```
src/Swap/BaseV1-core.sol:65:    event Mint(address indexed sender, uint amount0, uint amount1);
src/Swap/BaseV1-core.sol:66:    event Burn(address indexed sender, uint amount0, uint amount1, address indexed to);
src/Swap/BaseV1-core.sol:546:    event PairCreated(address indexed token0, address indexed token1, bool stable, address pair, uint);
src/Swap/BaseV1-core.sol:76:    event Claim(address indexed sender, address indexed recipient, uint amount0, uint amount1);
src/Swap/BaseV1-core.sol:78:    event Transfer(address indexed from, address indexed to, uint amount);
src/Swap/BaseV1-core.sol:79:    event Approval(address indexed owner, address indexed spender, uint amount);
```

------

## 4. require()/revert() statements should have descriptive reason strings

### Instances:


[BaseV1-periphery.sol:L465](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L465)
[BaseV1-periphery.sol:L468](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L468)
[BaseV1-core.sol:L556](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L556)
[BaseV1-core.sol:L561](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L561)
[BaseV1-core.sol:L562](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L562)
[BaseV1-core.sol:L576](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L576)
[BaseV1-core.sol:L581](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L581)
[BaseV1-core.sol:L586](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L586)
[BaseV1-core.sol:L100](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L100)
[BaseV1-core.sol:L108](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L108)
[BaseV1-periphery.sol:L219](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L219)
[BaseV1-periphery.sol:L90](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L90)
[BaseV1-periphery.sol:L220](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L220)
[BaseV1-periphery.sol:L300](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L300)
[BaseV1-core.sol:L342](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L342)
[BaseV1-core.sol:L523](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L523)
[BaseV1-core.sol:L526](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L526)

```
src/Swap/BaseV1-periphery.sol:465:        require(token.code.length > 0);
src/Swap/BaseV1-periphery.sol:468:        require(success && (data.length == 0 || abi.decode(data, (bool))));
src/Swap/BaseV1-core.sol:556:        require(msg.sender == admin);
src/Swap/BaseV1-core.sol:561:        require(msg.sender == admin);
src/Swap/BaseV1-core.sol:562:        require(newPeriod <= MaxPeriod);
src/Swap/BaseV1-core.sol:576:        require(msg.sender == pauser);
src/Swap/BaseV1-core.sol:581:        require(msg.sender == pendingPauser);
src/Swap/BaseV1-core.sol:586:        require(msg.sender == pauser);
src/Swap/BaseV1-core.sol:100:        require(msg.sender == factory);
src/Swap/BaseV1-core.sol:108:         require(_unlocked == 1);
src/Swap/BaseV1-periphery.sol:219:        require(amountADesired >= amountAMin);
src/Swap/BaseV1-periphery.sol:90:        require(msg.sender == admin);
src/Swap/BaseV1-periphery.sol:220:        require(amountBDesired >= amountBMin); 
src/Swap/BaseV1-periphery.sol:300:        require(IBaseV1Pair(pair).transferFrom(msg.sender, pair, liquidity)); // send liquidity to pair
src/Swap/BaseV1-core.sol:342:        require(!BaseV1Factory(factory).isPaused());
src/Swap/BaseV1-core.sol:523:        require(token.code.length > 0);
src/Swap/BaseV1-core.sol:526:        require(success && (data.length == 0 || abi.decode(data, (bool))));

```
-----

## 5. Use of Block.timestamp 

Block timestamps have historically been used for a variety of 
applications, such as entropy for random numbers (see the Entropy 
Illusion for further details), locking funds for periods of time, and 
various state-changing conditional statements that are time-dependent. 
Miners have the ability to adjust timestamps slightly, which can prove 
to be dangerous if block timestamps are used incorrectly in smart 
contracts.

### Instances
[BaseV1-core.sol:L96](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L96)
[BaseV1-core.sol:L138](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L138)
[BaseV1-core.sol:L159](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L159)
[BaseV1-core.sol:L176](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L176)
[BaseV1-core.sol:L180](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L180)
[BaseV1-core.sol:L471](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L471)
[BaseV1-periphery.sol:L71](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L71)

### Actual codes used:
```
src/Swap/BaseV1-core.sol:96:        observations.push(Observation(block.timestamp, 0, 0,0));
src/Swap/BaseV1-core.sol:138:        uint blockTimestamp = block.timestamp;
src/Swap/BaseV1-core.sol:159:        blockTimestamp = block.timestamp;
src/Swap/BaseV1-core.sol:176:        if (block.timestamp == _observation.timestamp) {
src/Swap/BaseV1-core.sol:180:        uint timeElapsed = block.timestamp - _observation.timestamp;
src/Swap/BaseV1-core.sol:471:        require(deadline >= block.timestamp, "BaseV1: EXPIRED");
src/Swap/BaseV1-periphery.sol:71:        require(deadline >= block.timestamp, "BaseV1Router: EXPIRED");

```

### Recommended Mitigation Steps

Block timestamps should not be used for entropy or generating random 
numbers—i.e., they should not be the deciding factor (either directly or
 through some derivation) for winning a game or changing an important 
state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

-----

## 6. Variable names that consist of all capital letters should be reserved for const/immutable variables


### Description: 

If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like this). 

### Instances :

[BaseV1-core.sol:L18](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L18)
[BaseV1-core.sol:L32](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L32)
[BaseV1-core.sol:L33](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L33)
[BaseV1-core.sol:L34](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L34)
[BaseV1-core.sol:L49](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L49)
[BaseV1-core.sol:L50](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L50)
[BaseV1-periphery.sol:L49](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L49)
[BaseV1-periphery.sol:L51](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L51)
[BaseV1-periphery.sol:L61](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L61)
[BaseV1-periphery.sol:L62](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L62)
[BaseV1-periphery.sol:L64](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L64)
[BaseV1-core.sol:L15](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L15)


```
src/Swap/BaseV1-core.sol:18:    bool public immutable stable;
src/Swap/BaseV1-core.sol:32:    address public immutable token0;
src/Swap/BaseV1-core.sol:33:    address public immutable token1;
src/Swap/BaseV1-core.sol:34:    address immutable factory;
src/Swap/BaseV1-core.sol:49:    uint internal immutable decimals0;
src/Swap/BaseV1-core.sol:50:    uint internal immutable decimals1;
src/Swap/BaseV1-periphery.sol:49:    address public immutable note;  
src/Swap/BaseV1-periphery.sol:51:    address public immutable Comptroller;
src/Swap/BaseV1-periphery.sol:61:    address public immutable factory;
src/Swap/BaseV1-periphery.sol:62:    IWCANTO public immutable wcanto;
src/Swap/BaseV1-periphery.sol:64:    bytes32 immutable pairCodeHash;
src/Swap/BaseV1-core.sol:15:    uint8 public constant decimals = 18;
                                                                    
```