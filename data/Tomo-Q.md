# QA Report

## ✅ L-1: Unsafe use of transfer()/transferFrom() with IERC20

### 📝 Description

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens.
example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert.

### 💡 Recommendation

Use OpenZeppelin's `SafeERC20` safeTransfer()/safeTransferFrom() instead

### 🔍 Findings:

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L282` [assert(wcanto.transfer(pair, amountCANTO));](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L282)

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L300` [require(IBaseV1Pair(pair).transferFrom(msg.sender, pair, liquidity)); // send liquidity to pair](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L300)

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L428` [assert(wcanto.transfer(pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]));](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L428)

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L475` [tokenCon.transferFrom(from, to, value);](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L475)

## ✅ L-2: `_safeMint()` should be used rather than `_mint()` wherever possible

### 📝 Description

`_mint()` is discouraged in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`.

### 💡 Recommendation

You should change from` _mint` to `_safeMint` in terms of security.

### 🔍 Findings:

`2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L305` [\_mint(address(0), MINIMUM_LIQUIDITY); // permanently lock the first MINIMUM_LIQUIDITY tokens](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L305)

`2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L311` [\_mint(to, liquidity);](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L311)

## ✅ L-3: `require()` should be used instead of `assert()`

### 📝 Description

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix

### 💡 Recommendation

You should change from `assert()` to `require()`

### 🔍 Findings:

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L85` [assert(msg.sender == address(wcanto)); // only accept ETH via fallback from the WETH contract](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L85)

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L236` [assert(amountAOptimal <= amountADesired);](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L236)

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L282` [assert(wcanto.transfer(pair, amountCANTO));](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L282)

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L428` [assert(wcanto.transfer(pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]));](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L428)

## ✅ L-4: Check zero denominator

### 📝 Description

When a division is computed, it must be ensured that the denominator is non-zero to prevent failure of the function call.

### 💡 Recommendation

You should change from `assert()` to `require()`

### 🔍 Findings:
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L193
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L234
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L268
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L584


## ✅ N-1: Consider addings checks for signature malleability

### 📝 Description

You should consider addings checks for signature malleability

### 💡 Recommendation

Use OpenZeppelin's `ECDSA` contract rather than calling `ecrecover()` directly

### 🔍 Findings:

`2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L488` [address recoveredAddress = ecrecover(digest, v, r, s);](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L488)

## ✅ N-2: No use of two-phase ownership transfers

### 📝 Description

Consider adding a two-phase transfer, where the current owner nominates the next owner, and the next owner has to call `accept*()` to become the new owner. This prevents passing the ownership to an account that is unable to use it.

### 💡 Recommendation

Consider implementing a two step process where the admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of admin to fully succeed. This ensures the nominated EOA account is a valid and active account.

### 🔍 Findings:

`2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L557` [admin = admin\_;](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L557)

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L91` [admin = admin\_;](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L91)

## ✅ N-3: Use a more recent version of solidity

### 📝 Description

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked (<bytes>, <bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked (<str>, <str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

### 💡 Recommendation

Use more recent version of solidity.

### 🔍 Findings:

`2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L2` [pragma solidity 0.8.11;](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L2)

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L3` [pragma solidity 0.8.11;](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L3)

## ✅ N-4: Use `string.concat()` or `bytes.concat()`

### 📝 Description

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)Solidity version 0.8.12 introduces `string.concat()`(vs `abi.encodePacked(<str>,<str>)`)

### 💡 Recommendation

Use concat instead of abi.encodePacked

### 🔍 Findings:

`2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L86` [name = string(abi.encodePacked("StableV1 AMM - ", erc20(\_token0).symbol(), "/", erc20(\_token1).symbol()));](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L86)

`2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L87` [symbol = string(abi.encodePacked("sAMM-", erc20(\_token0).symbol(), "/", erc20(\_token1).symbol()));](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L87)

`2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L89` [name = string(abi.encodePacked("VolatileV1 AMM - ", erc20(\_token0).symbol(), "/", erc20(\_token1).symbol()));](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L89)

`2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L90` [symbol = string(abi.encodePacked("vAMM-", erc20(\_token0).symbol(), "/", erc20(\_token1).symbol()));](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L90)

`2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L482` [abi.encodePacked(](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L482)

`2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L603` [bytes32 salt = keccak256(abi.encodePacked(token0, token1, stable)); // notice salt includes stable as well, 3 parameters](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L603)

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L103` [pair = address(uint160(uint256(keccak256(abi.encodePacked(](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L103)

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L106` [keccak256(abi.encodePacked(token0, token1, stable)),](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L106)

`2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L597` [return (keccak256(abi.encodePacked(str1)) == keccak256(abi.encodePacked(str2)));](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L597)
