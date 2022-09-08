## [L-01] REQUESTED PERIOD AND WINDOW SIZES ARE HARDCODED
Currently, the number of periods requested is hardcoded to 8 and the window size is hardcoded to 1 in `Swap\BaseV1-periphery.sol`. In case these numbers need to be adjusted in the future, it can be beneficial to set up admin functions for adjusting these. Please consider adding these functions for controlling state variables that represent the requested period and window sizes and using these state variables to replace the following hardcoded numbers.
```solidity
Swap\BaseV1-periphery.sol
  532: uint price = IBaseV1Pair(pair).quote(address(token), decimals, 8); // how much Canto is this asset worth?  
  544: uint price = IBaseV1Pair(pair).quote(address(token), decimals, 8);  
  562: (unitReserves, assetReserves) = pair.sampleReserves(8, 1);  
  566: (assetReserves, unitReserves) = pair.sampleReserves(8, 1);  
  572: (unitReserves, assetReserves) = pair.sampleReserves(8, 1);  
  576: (assetReserves, unitReserves) = pair.sampleReserves(8, 1);  
  581: for(uint i; i < 8; ++i) {
  586: uint LpPrice = LpPricesCumulative / 8; // take the average of the cumulative prices
```

## [L-02] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, constants can be used instead of magic numbers. Please consider replacing the magic numbers, such as `1e18`, used in the following code with constants.
```solidity
Swap\BaseV1-core.sol
  441: uint _x = x * 1e18 / decimals0; 
  442: uint _y = y * 1e18 / decimals1; 
  443: uint _a = (_x * _y) / 1e18; 
  444: uint _b = ((_x * _x) / 1e18 + (_y * _y) / 1e18); 
  445: return _a * _b / 1e18;  // x3y+y3x >= k 

Swap\BaseV1-periphery.sol
  499: return 1e18; // Stable coins supported by the lending market are instantiated by governance and their price will always be 1 note   
  503: return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller   
  507: return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller   
  520: return getPriceCanto(underlying) * getPriceNote(address(wcanto), false) / 1e18;  
  533: return price * 1e18 / decimals; //return the scaled price   
  545: return price * 1e18 / decimals; // divide by decimals now to maintain precision   
  584: LpPricesCumulative += (token0TVL + token1TVL) * 1e18 / supply[i];   
  592: return LpPrice * getPriceNote(address(wcanto), false) / 1e18; // return the price in terms of Note  
```

## [N-01] REDUNDANT CAST
In the following code, `underlying()` already returns an `address` type. Hence, `ICErc20(address(ctoken)).underlying()` does not need to be converted to `address` again.

```solidity
Swap\BaseV1-periphery.sol
  495: underlying = address(ICErc20(address(ctoken)).underlying()); // We are getting the price for a CErc20 lending market
```

## [N-02] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.
```solidity
Swap\BaseV1-core.sol
  137: function _update(uint balance0, uint balance1, uint _reserve0, uint _reserve1, uint _totalSupply) internal {
  201: function sample(address tokenIn, uint amountIn, uint points, uint window) public view returns (uint[] memory) {
  224: function reserves(uint granularity) external view returns(uint, uint) {
  237: function sampleReserves(uint points, uint window) public view returns (uint[] memory, uint[] memory) {
  260: function totalSupplyAvg(uint granularity) external view returns(uint) {
  271: function sampleSupply(uint points, uint window) public view returns (uint[] memory) {
  424: function _getAmountOut(uint amountIn, address tokenIn, uint _reserve0, uint _reserve1) internal view returns (uint) {

Swap\BaseV1-periphery.sol
  487: function getUnderlyingPrice(CToken ctoken) external override view returns(uint) {
  525: function getPriceCanto(address token_) internal view returns(uint) {
  537: function getPriceNote(address token_, bool stable) internal view returns(uint) {
  549: function getPriceLP(IBaseV1Pair pair) internal view returns(uint) {
```

## [N-03] COMMENTED OUT CODE
Commented out code can cause confusion. If the following code are not used anymore, please remove them for readability and maintainability.
```solidity
Swap\BaseV1-core.sol
  420: //amountIn -= amountIn / 10000; // remove fee from amount received
```

## [N-04] uint256 CAN BE USED INSTEAD OF uint
Both `uint` and `uint256` are used in `Swap\BaseV1-core.sol` and `Swap\BaseV1-periphery.sol`. For explicitness and consistency, please consider using `uint256` instead `uint` in the following code.
```solidity
Swap\BaseV1-core.sol
  137: function _update(uint balance0, uint balance1, uint _reserve0, uint _reserve1, uint _totalSupply) internal {
  138: uint blockTimestamp = block.timestamp;  
  139: uint timeElapsed = blockTimestamp - blockTimestampLast; 
  187: function quote(address tokenIn, uint amountIn, uint granularity) external view returns (uint amountOut) {
  188: uint [] memory _prices = sample(tokenIn, amountIn, granularity, 1);  
  189: uint priceAverageCumulative;  
  190: for (uint i = 0; i < _prices.length; i++) {  
  202: uint[] memory _prices = new uint[](points);  
  204: uint lastIndex = observations.length-1;  
  208: uint i = lastIndex - (points * window); // point from which to begin the sample  
  209: uint nextIndex = 0;  
  210: uint index = 0;  
  214: uint timeElapsed = observations[nextIndex].timestamp - observations[i].timestamp;  
  216: uint _reserve1 = (observations[nextIndex].reserve1Cumulative - observations[i].reserve1Cumulative) / timeElapsed;  
  224: function reserves(uint granularity) external view returns(uint, uint) {
  225: (uint[] memory _reserves0, uint[] memory _reserves1)= sampleReserves(granularity, 1);  
  226: uint reserveAverageCumulative0;  
  227: uint reserveAverageCumulative1;  
  229: for (uint i = 0; i < _reserves0.length; ++i) {  
  237: function sampleReserves(uint points, uint window) public view returns (uint[] memory, uint[] memory) {
  238: uint[] memory _reserves0 = new uint[](points);  
  239: uint[] memory _reserves1 = new uint[](points);  
  241: uint lastIndex = observations.length-1;  
  243: uint i = lastIndex - (points * window); // point from which to begin the sample  
  244: uint nextIndex = 0;  
  245: uint index = 0;  
  246: uint timeElapsed;  
  260: function totalSupplyAvg(uint granularity) external view returns(uint) {
  261: uint[] memory _totalSupplyAvg = sampleSupply(granularity, 1);  
  262: uint totalSupplyCumulativeAvg;  
  264: for (uint i = 0; i < _totalSupplyAvg.length; ++i) {  
  271: function sampleSupply(uint points, uint window) public view returns (uint[] memory) {
  272: uint[] memory _totalSupply = new uint[](points);  
  274: uint lastIndex = observations.length-1;  
  276: uint i = lastIndex - (points * window); // point from which to begin the sample  
  277: uint nextIndex = 0;  
  278: uint index = 0;  
  279: uint timeElapsed;  

Swap\BaseV1-periphery.sol
  487: function getUnderlyingPrice(CToken ctoken) external override view returns(uint) {
  502: uint decimals = erc20(underlying).decimals();  
  506: uint decimals = erc20(underlying).decimals();  
  525: function getPriceCanto(address token_) internal view returns(uint) {
  531: uint decimals = 10 ** token.decimals(); // get decimals of token
  532: uint price = IBaseV1Pair(pair).quote(address(token), decimals, 8); // how much Canto is this asset worth?
  537: function getPriceNote(address token_, bool stable) internal view returns(uint) {
  543: uint decimals = 10 ** token.decimals(); 
  544: uint price = IBaseV1Pair(pair).quote(address(token), decimals, 8); 
  549: function getPriceLP(IBaseV1Pair pair) internal view returns(uint) {
  550: uint[] memory supply = pair.sampleSupply(8, 1); 
  551: uint[] memory prices;  
  552: uint[] memory unitReserves;  
  553: uint[] memory assetReserves;  
  556: uint decimals; 
  579: uint LpPricesCumulative; 
  581: for(uint i; i < 8; ++i) {
  582: uint token0TVL = assetReserves[i] * (prices[i] / decimals);
  583: uint token1TVL = unitReserves[i]; // price of the unit asset is always 1 
  586: uint LpPrice = LpPricesCumulative / 8; // take the average of the cumulative prices
```

## [N-05] NEWER VERSION OF SOLIDITY CAN BE USED
The protocol can benefit from more fixes and gas-efficient features by using a newer version of Solidity. Changes for newer Solidity versions can be found in https://blog.soliditylang.org/category/releases/. Please consider updating the versions for the following files.
```solidity
Swap\BaseV1-core.sol
  2: pragma solidity 0.8.11;

Swap\BaseV1-libs.sol
  1: pragma solidity 0.8.11;

Swap\BaseV1-periphery.sol
  3: pragma solidity 0.8.11;
```