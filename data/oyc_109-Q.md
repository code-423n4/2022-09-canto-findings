## [L-01] No Transfer Ownership Pattern

Recommend considering implementing a two step process where the owner or admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L88-L92
https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L555-L558

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
BaseV1-core.sol::96 => observations.push(Observation(block.timestamp, 0, 0,0));
BaseV1-core.sol::138 => uint blockTimestamp = block.timestamp;
BaseV1-core.sol::159 => blockTimestamp = block.timestamp;
BaseV1-core.sol::176 => if (block.timestamp == _observation.timestamp) {
BaseV1-core.sol::180 => uint timeElapsed = block.timestamp - _observation.timestamp;
BaseV1-core.sol::471 => require(deadline >= block.timestamp, "BaseV1: EXPIRED");
BaseV1-periphery.sol::71 => require(deadline >= block.timestamp, "BaseV1Router: EXPIRED");
```

## [L-03] decimals() not part of ERC20 standard

decimals() is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

```
BaseV1-core.sol::93 => decimals0 = 10**erc20(_token0).decimals();
BaseV1-core.sol::94 => decimals1 = 10**erc20(_token1).decimals();
BaseV1-periphery.sol::502 => uint decimals = erc20(underlying).decimals();
BaseV1-periphery.sol::506 => uint decimals = erc20(underlying).decimals();
BaseV1-periphery.sol::531 => uint decimals = 10 ** token.decimals(); // get decimals of token
BaseV1-periphery.sol::543 => uint decimals = 10 ** token.decimals();
BaseV1-periphery.sol::560 => decimals = 10 ** (erc20(token1).decimals()); // we must normalize the price of token1 to 18 decimals
BaseV1-periphery.sol::564 => decimals = 10 ** (erc20(token0).decimals());
BaseV1-periphery.sol::570 => decimals = 10 ** (erc20(token1).decimals());
BaseV1-periphery.sol::574 => decimals = 10 ** (erc20(token0)).decimals();
```

## [L-04] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). Unless there is a compelling reason, abi.encode should be preferred. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

```
BaseV1-core.sol::603 => bytes32 salt = keccak256(abi.encodePacked(token0, token1, stable)); // notice salt includes stable as well, 3 parameters
BaseV1-periphery.sol::103 => pair = address(uint160(uint256(keccak256(abi.encodePacked(
BaseV1-periphery.sol::106 => keccak256(abi.encodePacked(token0, token1, stable)),
BaseV1-periphery.sol::597 => return (keccak256(abi.encodePacked(str1)) == keccak256(abi.encodePacked(str2)));
```

## [L-05] require() should be used instead of assert()

require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking

```
BaseV1-periphery.sol::85 => assert(msg.sender == address(wcanto)); // only accept ETH via fallback from the WETH contract
BaseV1-periphery.sol::236 => assert(amountAOptimal <= amountADesired);
BaseV1-periphery.sol::282 => assert(wcanto.transfer(pair, amountCANTO));
BaseV1-periphery.sol::428 => assert(wcanto.transfer(pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]));
```

## [L-06] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
BaseV1-periphery.sol::282 => assert(wcanto.transfer(pair, amountCANTO));
BaseV1-periphery.sol::300 => require(IBaseV1Pair(pair).transferFrom(msg.sender, pair, liquidity)); // send liquidity to pair
BaseV1-periphery.sol::428 => assert(wcanto.transfer(pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]));
BaseV1-periphery.sol::475 => tokenCon.transferFrom(from, to, value);
```

## [L-07] Missing Zero address checks

Zero-address checks are a best practice for input validation of critical address parameters. While the codebase applies this to most cases, there are many places where this is missing in constructors and setters.
Impact: Accidental use of zero-addresses may result in exceptions, burn fees/tokens, or force redeployment of contracts.

```
BaseV1-periphery.sol::76 => factory = _factory;
BaseV1-periphery.sol::91 => admin = admin_;
```

## [L-08] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
BaseV1-core.sol::99 => function setPeriodSize(uint periodSize_) external {
BaseV1-core.sol::555 => function setAdmin(address admin_) external {
BaseV1-core.sol::560 => function setPeriodSize(uint newPeriod) external {
BaseV1-core.sol::575 => function setPauser(address _pauser) external {
BaseV1-core.sol::585 => function setPause(bool _state) external {
BaseV1-periphery.sol::89 => function setAdmin(address admin_) external {
BaseV1-periphery.sol::478 => function setStable(address underlying) external returns (uint) {
```

## [N-1] Use a more recent version of solidity

Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

```
BaseV1-core.sol::2 => pragma solidity 0.8.11;
BaseV1-periphery.sol::3 => pragma solidity 0.8.11;
```

## [N-2] require()/revert() statements should have descriptive reason strings

Descriptive reason strings should be used so that users can troubleshot any reverted calls

```
BaseV1-core.sol::100 => require(msg.sender == factory);
BaseV1-core.sol::108 => require(_unlocked == 1);
BaseV1-core.sol::342 => require(!BaseV1Factory(factory).isPaused());
BaseV1-core.sol::523 => require(token.code.length > 0);
BaseV1-core.sol::556 => require(msg.sender == admin);
BaseV1-core.sol::561 => require(msg.sender == admin);
BaseV1-core.sol::562 => require(newPeriod <= MaxPeriod);
BaseV1-core.sol::576 => require(msg.sender == pauser);
BaseV1-core.sol::581 => require(msg.sender == pendingPauser);
BaseV1-core.sol::586 => require(msg.sender == pauser);
BaseV1-periphery.sol::90 => require(msg.sender == admin);
BaseV1-periphery.sol::219 => require(amountADesired >= amountAMin);
BaseV1-periphery.sol::220 => require(amountBDesired >= amountBMin);
BaseV1-periphery.sol::465 => require(token.code.length > 0);
```

## [N-3] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
BaseV1-core.sol::75 => event Sync(uint reserve0, uint reserve1);
```

## [N-4] Missing NatSpec

Code should include NatSpec

```
BaseV1-core.sol::1 => // SPDX-License-Identifier: MIT
BaseV1-periphery.sol::1 => // SPDX-License-Identifier: MIT
```

## [N-5] Adding a return statement when the function defines a named return variable, is redundant

It is not necessary to have both a named return and a return statement.

```
BaseV1-core.sol::122 => function metadata() external view returns (uint dec0, uint dec1, uint r0, uint r1, bool st, address t0, address t1) {
BaseV1-core.sol::187 => function quote(address tokenIn, uint amountIn, uint granularity) external view returns (uint amountOut) {
BaseV1-periphery.sol::126 => function getAmountOut(uint amountIn, address tokenIn, address tokenOut) external view returns (uint amount, bool stable) {
BaseV1-periphery.sol::194 => ) public view returns (uint amountA, uint amountB) {
```

## [N-6] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
BaseV1-core.sol::99 => function setPeriodSize(uint periodSize_) external {
BaseV1-core.sol::555 => function setAdmin(address admin_) external {
BaseV1-core.sol::560 => function setPeriodSize(uint newPeriod) external {
BaseV1-core.sol::575 => function setPauser(address _pauser) external {
BaseV1-core.sol::585 => function setPause(bool _state) external {
BaseV1-periphery.sol::89 => function setAdmin(address admin_) external {
BaseV1-periphery.sol::478 => function setStable(address underlying) external returns (uint) {
```