1.	Use same code convention(spacing and tabulation) through the code. Add spaces here between signes.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L225
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L241
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L248
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L274
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L281
2.	Remove empty comment.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L231
3.	Do not initialize with 0. It will be done by default.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L244-L245
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L277-L278
4.	Use any kind of named params(or constant) instead of magic numbers for clarity.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L225
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L261
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L544
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L532
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L550
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L561-L562
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L565-L566
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L571-L572
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L575-L576
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L581
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L586
5.	Check `ctoken` for zero address.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L277-L278
6.	Avoid redundant cast, pass `underlying` param instead of `address(wcanto)`.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L493
7.	Avoid redundant cast, pass ` underlying = ICErc20(address(ctoken)).underlying() ` param instead of ` underlying = address(ICErc20(address(ctoken)).underlying())`.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L495
8.	Avoid redundant cast, pass `token_` param instead of `address(token)`.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L527
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L532
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L539
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L544
9.	Use `else if ((compareStrings(symbol, "cUSDT") || compareStrings(symbol, "cUSDC")) && (msg.sender == Comptroller ))` condition instead of creating 2 similar if blocks.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L501
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L505
10.	Use same code convention(spacing and tabulation) for if else blocks.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L491-L496
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L498-L508
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L511-L521
11.	Cash `pair.stable()` value as you use it on the line `588` again.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L558
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L588
