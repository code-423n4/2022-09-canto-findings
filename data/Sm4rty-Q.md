

## 1. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom

It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

### Instances:
[BaseV1-periphery.sol:L282](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L282)
[BaseV1-periphery.sol:L428](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L428)
[BaseV1-periphery.sol:L300](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L300)
[BaseV1-periphery.sol:L475](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L475)
```
src/Swap/BaseV1-periphery.sol:282:        assert(wcanto.transfer(pair, amountCANTO));
src/Swap/BaseV1-periphery.sol:428:        assert(wcanto.transfer(pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]));
src/Swap/BaseV1-periphery.sol:300:        require(IBaseV1Pair(pair).transferFrom(msg.sender, pair, liquidity)); // send liquidity to pair
src/Swap/BaseV1-periphery.sol:475:        tokenCon.transferFrom(from, to, value);
``` 
### Reference:

This [similar medium-severity finding](https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call) from Consensys Diligence Audit of Fei Protocol.

### Recommended Mitigation Steps:

Consider using safeTransfer/safeTransferFrom or require() consistently.


-----


## 2. _safeMint() should be used rather than _mint() wherever possible

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

### Instances
[BaseV1-core.sol:L305](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L305)
[BaseV1-core.sol:L311](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L311)
```
src/Swap/BaseV1-core.sol:305:            _mint(address(0), MINIMUM_LIQUIDITY); // permanently lock the first MINIMUM_LIQUIDITY tokens
src/Swap/BaseV1-core.sol:311:        _mint(to, liquidity);
``` 
### Recommendations:

Use _safeMint() instead of _mint().

-----

## 3. Cross-Chain Replay Attacks

Storing the `block.chainid` is not safe. See [this](https://github.com/code-423n4/2021-04-maple-findings/issues/2) issue from a prior contest for details.

Ethereum fork in the feature, the `chained` will change however the one used by the permits will not be enabling a user to use any new permits on both chains thus breaking the token on the forked chain permanently.

### Instances:
[BaseV1-core.sol:L477](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L477)
```
src/Swap/BaseV1-core.sol:477:                block.chainid,
``` 
### References:

[https://github.com/code-423n4/2021-04-maple-findings/issues/2](https://github.com/code-423n4/2021-04-maple-findings/issues/2)

-----

## 4. Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Instances:
[BaseV1-core.sol:L96](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L96)
[BaseV1-core.sol:L138](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L138)
[BaseV1-core.sol:L159](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L159)
[BaseV1-core.sol:L176](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L176)
[BaseV1-core.sol:L180](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L180)
[BaseV1-core.sol:L471](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L471)
[BaseV1-periphery.sol:L71](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L71)
```
src/Swap/BaseV1-core.sol:96:        observations.push(Observation(block.timestamp, 0, 0,0));
src/Swap/BaseV1-core.sol:138:        uint blockTimestamp = block.timestamp;
src/Swap/BaseV1-core.sol:159:        blockTimestamp = block.timestamp;
src/Swap/BaseV1-core.sol:176:        if (block.timestamp == _observation.timestamp) {
src/Swap/BaseV1-core.sol:180:        uint timeElapsed = block.timestamp - _observation.timestamp;
src/Swap/BaseV1-core.sol:471:        require(deadline >= block.timestamp, "BaseV1: EXPIRED");
src/Swap/BaseV1-periphery.sol:71:        require(deadline >= block.timestamp, "BaseV1Router: EXPIRED");
```

### References:

https://github.com/code-423n4/2022-04-dualityfocus-findings/issues/33

-----

## 5. Variable names that consist of all capital letters should be reserved for const/immutable variables

Variable names that consist of all capital letters should be reserved for const/immutable variables

If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like this).

### Instance:
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

### References:
https://code4rena.com/reports/2022-05-sturdy#n-08-variable-names-that-consist-of-all-capital-letters-should-be-reserved-for-constimmutable-variables

-----

## 6. Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

### Instances:

There are the following instances of this issue:
[BaseV1-core.sol:L65](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L65)
[BaseV1-core.sol:L66](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L66)
[BaseV1-core.sol:L67](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L67)
[BaseV1-core.sol:L76](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L76)
[BaseV1-core.sol:L78](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L78)
[BaseV1-core.sol:L79](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L79)
[BaseV1-core.sol:L546](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L546)

```
src/Swap/BaseV1-core.sol:65:    event Mint(address indexed sender, uint amount0, uint amount1);
src/Swap/BaseV1-core.sol:66:    event Burn(address indexed sender, uint amount0, uint amount1, address indexed to);
src/Swap/BaseV1-core.sol:67:    event Swap(address indexed sender, uint amount0In,uint amount1In,uint amount0Out,uint amount1Out,address indexed to);
src/Swap/BaseV1-core.sol:76:    event Claim(address indexed sender, address indexed recipient, uint amount0, uint amount1);
src/Swap/BaseV1-core.sol:78:    event Transfer(address indexed from, address indexed to, uint amount);
src/Swap/BaseV1-core.sol:79:    event Approval(address indexed owner, address indexed spender, uint amount);
src/Swap/BaseV1-core.sol:546:    event PairCreated(address indexed token0, address indexed token1, bool stable, address pair, uint);
```

### References:

[https://code4rena.com/reports/2022-05-sturdy#n-10-event-is-missing-indexed-fields](https://code4rena.com/reports/2022-05-sturdy#n-10-event-is-missing-indexed-fields)

-----
## 7. require()/revert() statements should have descriptive reason strings

### Instances:
[BaseV1-periphery.sol:L90](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L90)
[BaseV1-periphery.sol:L219](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L219)
[BaseV1-periphery.sol:L220](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L220)
[BaseV1-periphery.sol:L300](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L300)
[BaseV1-periphery.sol:L465](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L465)
[BaseV1-periphery.sol:L468](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L468)
[BaseV1-core.sol:L100](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L100)
[BaseV1-core.sol:L108](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L108)
[BaseV1-core.sol:L342](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L342)
[BaseV1-core.sol:L523](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L523)
[BaseV1-core.sol:L526](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L526)
[BaseV1-core.sol:L556](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L556)
[BaseV1-core.sol:L561](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L561)
[BaseV1-core.sol:L562](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L562)
[BaseV1-core.sol:L576](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L576)
[BaseV1-core.sol:L581](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L581)
[BaseV1-core.sol:L586](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L586)

```
src/Swap/BaseV1-periphery.sol:90:        require(msg.sender == admin);
src/Swap/BaseV1-periphery.sol:219:        require(amountADesired >= amountAMin);
src/Swap/BaseV1-periphery.sol:220:        require(amountBDesired >= amountBMin);
src/Swap/BaseV1-periphery.sol:300:        require(IBaseV1Pair(pair).transferFrom(msg.sender, pair, liquidity)); // send liquidity to pair
src/Swap/BaseV1-periphery.sol:465:        require(token.code.length > 0);
src/Swap/BaseV1-periphery.sol:468:        require(success && (data.length == 0 || abi.decode(data, (bool))));
src/Swap/BaseV1-core.sol:100:        require(msg.sender == factory);
src/Swap/BaseV1-core.sol:108:        require(_unlocked == 1);
src/Swap/BaseV1-core.sol:342:        require(!BaseV1Factory(factory).isPaused());
src/Swap/BaseV1-core.sol:523:        require(token.code.length > 0);
src/Swap/BaseV1-core.sol:526:        require(success && (data.length == 0 || abi.decode(data, (bool))));
src/Swap/BaseV1-core.sol:556:        require(msg.sender == admin);
src/Swap/BaseV1-core.sol:561:        require(msg.sender == admin);
src/Swap/BaseV1-core.sol:562:        require(newPeriod <= MaxPeriod);
src/Swap/BaseV1-core.sol:576:        require(msg.sender == pauser);
src/Swap/BaseV1-core.sol:581:        require(msg.sender == pendingPauser);
src/Swap/BaseV1-core.sol:586:        require(msg.sender == pauser);
```

### References:
https://code4rena.com/reports/2022-04-phuture/#n-02-requirerevert-statements-should-have-descriptive-reason-strings

----
