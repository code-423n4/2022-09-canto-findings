

### [L01] require() should be used instead of assert()


#### Findings:
```
2022-09-canto/src/Swap/BaseV1-periphery.sol::85 => assert(msg.sender == address(wcanto)); // only accept ETH via fallback from the WETH contract
2022-09-canto/src/Swap/BaseV1-periphery.sol::236 => assert(amountAOptimal <= amountADesired);
2022-09-canto/src/Swap/BaseV1-periphery.sol::282 => assert(wcanto.transfer(pair, amountCANTO));
2022-09-canto/src/Swap/BaseV1-periphery.sol::428 => assert(wcanto.transfer(pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]));
```





### [L02] Unbounded loops with external calls

#### Impact
The interface and the function should require a start index and a 
lenght, so that the index composition can be fetched in batches without 
running out of gas. If there are thousands of index components (e.g. 
like the Wilshire 5000 index), the function may revert
#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::197 => function prices(address tokenIn, uint amountIn, uint points) external view returns (uint[] memory) {
2022-09-canto/src/Swap/BaseV1-core.sol::201 => function sample(address tokenIn, uint amountIn, uint points, uint window) public view returns (uint[] memory) {
2022-09-canto/src/Swap/BaseV1-core.sol::237 => function sampleReserves(uint points, uint window) public view returns (uint[] memory, uint[] memory) {
2022-09-canto/src/Swap/BaseV1-core.sol::271 => function sampleSupply(uint points, uint window) public view returns (uint[] memory) {
2022-09-canto/src/Swap/BaseV1-periphery.sol::31 => function sampleReserves(uint points, uint window) external view returns(uint[] memory, uint[] memory);
2022-09-canto/src/Swap/BaseV1-periphery.sol::32 => function sampleSupply(uint points, uint window) external view returns(uint[] memory);
2022-09-canto/src/Swap/BaseV1-periphery.sol::33 => function sample(address tokenIn, uint amountIn, uint points, uint window) external view returns(uint[] memory);
2022-09-canto/src/Swap/BaseV1-periphery.sol::141 => function getAmountsOut(uint amountIn, route[] memory routes) public view returns (uint[] memory amounts) {
2022-09-canto/src/Swap/BaseV1-periphery.sol::370 => function _swap(uint[] memory amounts, route[] memory routes, address _to) internal virtual {
2022-09-canto/src/Swap/BaseV1-periphery.sol::418 => function swapExactCANTOForTokens(uint amountOutMin, route[] calldata routes, address to, uint deadline)
2022-09-canto/src/Swap/BaseV1-periphery.sol::432 => function swapExactTokensForCANTO(uint amountIn, uint amountOutMin, route[] calldata routes, address to, uint deadline)
```


### [L03] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::101 => periodSize = periodSize_;
2022-09-canto/src/Swap/BaseV1-core.sol::131 => _reserve0 = reserve0;
2022-09-canto/src/Swap/BaseV1-core.sol::132 => _reserve1 = reserve1;
2022-09-canto/src/Swap/BaseV1-core.sol::133 => _blockTimestampLast = blockTimestampLast;
2022-09-canto/src/Swap/BaseV1-core.sol::557 => admin = admin_;
2022-09-canto/src/Swap/BaseV1-core.sol::587 => isPaused = _state;
2022-09-canto/src/Swap/BaseV1-periphery.sol::76 => factory = _factory;
2022-09-canto/src/Swap/BaseV1-periphery.sol::77 => pairCodeHash = IBaseV1Factory(_factory).pairCodeHash();
2022-09-canto/src/Swap/BaseV1-periphery.sol::78 => wcanto = IWCANTO(_wcanto);
2022-09-canto/src/Swap/BaseV1-periphery.sol::79 => note = note_;
2022-09-canto/src/Swap/BaseV1-periphery.sol::80 => Comptroller = Comptroller_;
2022-09-canto/src/Swap/BaseV1-periphery.sol::91 => admin = admin_;
```











### [L04] Unused `receive()` function will lock Ether in contract

#### Impact
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert
#### Findings:
```

2022-09-canto/src/Swap/BaseV1-periphery.sol::84 => receive() external payable {
```






### [L05] `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`

#### Impact
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::603 => bytes32 salt = keccak256(abi.encodePacked(token0, token1, stable)); // notice salt includes stable as well, 3 parameters
2022-09-canto/src/Swap/BaseV1-periphery.sol::103 => pair = address(uint160(uint256(keccak256(abi.encodePacked(
2022-09-canto/src/Swap/BaseV1-periphery.sol::106 => keccak256(abi.encodePacked(token0, token1, stable)),
2022-09-canto/src/Swap/BaseV1-periphery.sol::597 => return (keccak256(abi.encodePacked(str1)) == keccak256(abi.encodePacked(str2)));
```




### [L06] `_safeMint()` should be used rather than `_mint()` wherever possible



#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::305 => _mint(address(0), MINIMUM_LIQUIDITY); // permanently lock the first MINIMUM_LIQUIDITY tokens
2022-09-canto/src/Swap/BaseV1-core.sol::311 => _mint(to, liquidity);
```




#### Non-Critical Issues



### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::221 => return _prices;
2022-09-canto/src/Swap/BaseV1-core.sol::257 => return (_reserves0, _reserves1);
2022-09-canto/src/Swap/BaseV1-core.sol::288 => return _totalSupply;
2022-09-canto/src/Swap/BaseV1-core.sol::421 => return _getAmountOut(amountIn, tokenIn, _reserve0, _reserve1);
2022-09-canto/src/Swap/BaseV1-periphery.sol::589 => return LpPrice;
```





### [N02] `require()`/`revert()` statements should have descriptive reason strings


#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::100 => require(msg.sender == factory);
2022-09-canto/src/Swap/BaseV1-core.sol::108 => require(_unlocked == 1);
2022-09-canto/src/Swap/BaseV1-core.sol::342 => require(!BaseV1Factory(factory).isPaused());
2022-09-canto/src/Swap/BaseV1-core.sol::523 => require(token.code.length > 0);
2022-09-canto/src/Swap/BaseV1-core.sol::526 => require(success && (data.length == 0 || abi.decode(data, (bool))));
2022-09-canto/src/Swap/BaseV1-core.sol::556 => require(msg.sender == admin);
2022-09-canto/src/Swap/BaseV1-core.sol::561 => require(msg.sender == admin);
2022-09-canto/src/Swap/BaseV1-core.sol::562 => require(newPeriod <= MaxPeriod);
2022-09-canto/src/Swap/BaseV1-core.sol::576 => require(msg.sender == pauser);
2022-09-canto/src/Swap/BaseV1-core.sol::581 => require(msg.sender == pendingPauser);
2022-09-canto/src/Swap/BaseV1-core.sol::586 => require(msg.sender == pauser);
2022-09-canto/src/Swap/BaseV1-periphery.sol::90 => require(msg.sender == admin);
2022-09-canto/src/Swap/BaseV1-periphery.sol::219 => require(amountADesired >= amountAMin);
2022-09-canto/src/Swap/BaseV1-periphery.sol::220 => require(amountBDesired >= amountBMin);
2022-09-canto/src/Swap/BaseV1-periphery.sol::300 => require(IBaseV1Pair(pair).transferFrom(msg.sender, pair, liquidity)); // send liquidity to pair
2022-09-canto/src/Swap/BaseV1-periphery.sol::465 => require(token.code.length > 0);
2022-09-canto/src/Swap/BaseV1-periphery.sol::468 => require(success && (data.length == 0 || abi.decode(data, (bool))));
```





### [N03] `constants` should be defined rather than using magic numbers


#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::15 => uint8 public constant decimals = 18;
2022-09-canto/src/Swap/BaseV1-core.sol::30 => uint internal constant MINIMUM_LIQUIDITY = 10**3;
2022-09-canto/src/Swap/BaseV1-core.sol::45 => uint public periodSize = 1800;
2022-09-canto/src/Swap/BaseV1-core.sol::93 => decimals0 = 10**erc20(_token0).decimals();
2022-09-canto/src/Swap/BaseV1-core.sol::94 => decimals1 = 10**erc20(_token1).decimals();
2022-09-canto/src/Swap/BaseV1-core.sol::387 => return x0*(y*y/1e18*y/1e18)/1e18+(x0*x0/1e18*x0/1e18)*y/1e18;
2022-09-canto/src/Swap/BaseV1-core.sol::391 => return 3*x0*(y*y/1e18)/1e18+(x0*x0/1e18*x0/1e18);
2022-09-canto/src/Swap/BaseV1-core.sol::399 => uint dy = (xy - k)*1e18/_d(x0, y);
2022-09-canto/src/Swap/BaseV1-core.sol::402 => uint dy = (k - xy)*1e18/_d(x0, y);
2022-09-canto/src/Swap/BaseV1-core.sol::427 => _reserve0 = _reserve0 * 1e18 / decimals0;
2022-09-canto/src/Swap/BaseV1-core.sol::428 => _reserve1 = _reserve1 * 1e18 / decimals1;
2022-09-canto/src/Swap/BaseV1-core.sol::430 => amountIn = tokenIn == token0 ? amountIn * 1e18 / decimals0 : amountIn * 1e18 / decimals1;
2022-09-canto/src/Swap/BaseV1-core.sol::432 => return y * (tokenIn == token0 ? decimals1 : decimals0) / 1e18;
2022-09-canto/src/Swap/BaseV1-core.sol::441 => uint _x = x * 1e18 / decimals0;
2022-09-canto/src/Swap/BaseV1-core.sol::442 => uint _y = y * 1e18 / decimals1;
2022-09-canto/src/Swap/BaseV1-core.sol::443 => uint _a = (_x * _y) / 1e18;
2022-09-canto/src/Swap/BaseV1-core.sol::444 => uint _b = ((_x * _x) / 1e18 + (_y * _y) / 1e18);
2022-09-canto/src/Swap/BaseV1-core.sol::445 => return _a * _b / 1e18;  // x3y+y3x >= k
2022-09-canto/src/Swap/BaseV1-core.sol::447 => return x * y; // xy >= k
2022-09-canto/src/Swap/BaseV1-periphery.sol::63 => uint internal constant MINIMUM_LIQUIDITY = 10**3;
2022-09-canto/src/Swap/BaseV1-periphery.sol::499 => return 1e18; // Stable coins supported by the lending market are instantiated by governance and their price will always be 1 note
2022-09-canto/src/Swap/BaseV1-periphery.sol::503 => return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller
2022-09-canto/src/Swap/BaseV1-periphery.sol::507 => return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller
2022-09-canto/src/Swap/BaseV1-periphery.sol::520 => return getPriceCanto(underlying) * getPriceNote(address(wcanto), false) / 1e18;
2022-09-canto/src/Swap/BaseV1-periphery.sol::531 => uint decimals = 10 ** token.decimals(); // get decimals of token
2022-09-canto/src/Swap/BaseV1-periphery.sol::533 => return price * 1e18 / decimals; //return the scaled price
2022-09-canto/src/Swap/BaseV1-periphery.sol::543 => uint decimals = 10 ** token.decimals();
2022-09-canto/src/Swap/BaseV1-periphery.sol::545 => return price * 1e18 / decimals; // divide by decimals now to maintain precision
2022-09-canto/src/Swap/BaseV1-periphery.sol::560 => decimals = 10 ** (erc20(token1).decimals()); // we must normalize the price of token1 to 18 decimals
2022-09-canto/src/Swap/BaseV1-periphery.sol::564 => decimals = 10 ** (erc20(token0).decimals());
2022-09-canto/src/Swap/BaseV1-periphery.sol::570 => decimals = 10 ** (erc20(token1).decimals());
2022-09-canto/src/Swap/BaseV1-periphery.sol::574 => decimals = 10 ** (erc20(token0)).decimals();
2022-09-canto/src/Swap/BaseV1-periphery.sol::584 => LpPricesCumulative += (token0TVL + token1TVL) * 1e18 / supply[i];
2022-09-canto/src/Swap/BaseV1-periphery.sol::592 => return LpPrice * getPriceNote(address(wcanto), false) / 1e18; // return the price in terms of Note
```





### [N04] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)
#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::2 => pragma solidity 0.8.11;
2022-09-canto/src/Swap/BaseV1-periphery.sol::3 => pragma solidity 0.8.11;
```





### [N05] Variable names that consist of all capital letters should be reserved for `const`/`immutable `variables

#### Impact
If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::25 => bytes32 internal DOMAIN_SEPARATOR;
```


### [N06] Event is missing `indexed` fields

#### Impact
Each `event` should use three `indexed` fields if there are three or more fields
#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::65 => event Mint(address indexed sender, uint amount0, uint amount1);
2022-09-canto/src/Swap/BaseV1-core.sol::66 => event Burn(address indexed sender, uint amount0, uint amount1, address indexed to);
2022-09-canto/src/Swap/BaseV1-core.sol::75 => event Sync(uint reserve0, uint reserve1);
2022-09-canto/src/Swap/BaseV1-core.sol::76 => event Claim(address indexed sender, address indexed recipient, uint amount0, uint amount1);
2022-09-canto/src/Swap/BaseV1-core.sol::78 => event Transfer(address indexed from, address indexed to, uint amount);
2022-09-canto/src/Swap/BaseV1-core.sol::79 => event Approval(address indexed owner, address indexed spender, uint amount);
2022-09-canto/src/Swap/BaseV1-core.sol::546 => event PairCreated(address indexed token0, address indexed token1, bool stable, address pair, uint);
```





### [N07] Unused file

#### Impact

#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::1 => // SPDX-License-Identifier: MIT
2022-09-canto/src/Swap/BaseV1-periphery.sol::1 => // SPDX-License-Identifier: MIT
```






### [N08] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from external to public.
#### Findings:
```

2022-09-canto/src/Swap/BaseV1-core.sol::118 => function lastObservation() public view returns (Observation memory) {
2022-09-canto/src/Swap/BaseV1-core.sol::130 => function getReserves() public view returns (uint _reserve0, uint _reserve1, uint _blockTimestampLast) {
2022-09-canto/src/Swap/BaseV1-core.sol::158 => function currentCumulativePrices() public view returns (uint reserve0Cumulative, uint reserve1Cumulative, uint blockTimestamp) {
2022-09-canto/src/Swap/BaseV1-core.sol::201 => function sample(address tokenIn, uint amountIn, uint points, uint window) public view returns (uint[] memory) {
2022-09-canto/src/Swap/BaseV1-core.sol::237 => function sampleReserves(uint points, uint window) public view returns (uint[] memory, uint[] memory) {
2022-09-canto/src/Swap/BaseV1-core.sol::271 => function sampleSupply(uint points, uint window) public view returns (uint[] memory) {
2022-09-canto/src/Swap/BaseV1-core.sol::439 => function _k(uint x, uint y) public view returns (uint) {
2022-09-canto/src/Swap/BaseV1-periphery.sol::94 => function sortTokens(address tokenA, address tokenB) public pure returns (address token0, address token1) {
2022-09-canto/src/Swap/BaseV1-periphery.sol::101 => function pairFor(address tokenA, address tokenB, bool stable) public view returns (address pair) {
2022-09-canto/src/Swap/BaseV1-periphery.sol::119 => function getReserves(address tokenA, address tokenB, bool stable) public view returns (uint reserveA, uint reserveB) {
2022-09-canto/src/Swap/BaseV1-periphery.sol::141 => function getAmountsOut(uint amountIn, route[] memory routes) public view returns (uint[] memory amounts) {
2022-09-canto/src/Swap/BaseV1-periphery.sol::153 => function isPair(address pair) public view returns (bool) {
```





### [N09] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks
#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::36 => // Structure to capture time period obervations every 30 minutes, used for local oracles
2022-09-canto/src/Swap/BaseV1-core.sol::44 => // Capture oracle reading every 30 minutes
2022-09-canto/src/Swap/BaseV1-core.sol::147 => timeElapsed = blockTimestamp - _point.timestamp; // compare the last observation with current timestamp, if greater than 30 minutes, record a new event
```





### [N10]  Constant redefined elsewhere

#### Impact
Consider defining in only one contract so that values cannot become out of sync when only one location is updated
#### Findings:
```

2022-09-canto/src/Swap/BaseV1-core.sol::481 => bytes32 digest = keccak256(
2022-09-canto/src/Swap/BaseV1-core.sol::603 => bytes32 salt = keccak256(abi.encodePacked(token0, token1, stable)); // notice salt includes stable as well, 3 parameters
```







### [N11] Interfaces should be moved to separate files

#### Impact
Most of the other interfaces in this project are in their own file in
 the interfaces directory. The interfaces below do not follow this 
pattern
#### Findings:
```
2022-09-canto/src/Swap/BaseV1-core.sol::6 => interface IBaseV1Callee {
2022-09-canto/src/Swap/BaseV1-periphery.sol::9 => interface IBaseV1Factory {
2022-09-canto/src/Swap/BaseV1-periphery.sol::17 => interface IBaseV1Pair {
2022-09-canto/src/Swap/BaseV1-periphery.sol::37 => interface IWCANTO {
2022-09-canto/src/Swap/BaseV1-periphery.sol::43 => interface ICErc20 {
```





