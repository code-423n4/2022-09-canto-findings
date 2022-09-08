# Summary
## Low
[L01] Outdated compiler version
[L02] Functions should check division by 0 when it's possible

## Non Critical
[NC01] Use of block timestamp
[NC02] Unnecesary use of variable

# Low
## [L01] Outdated compiler version
### Description
It's a best practice to use the latest compiler version. The actual compiled version is 0.8.11 (quite old) and the latest is 0.8.16.

### Mitigation
Update the version of solidity to 0.8.16.

### Lines in the code
[BaseV1-core.sol#L2](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L2)
[BaseV1-periphery.sol#L3](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L3)

## [L02] Functions should check division by 0 when it's possible
### Description
In solidity when a number is divided by 0 the transaction is reverted due to an exception.

### Proof of concept

```diff
contract Test {

    function TestDivision (uint parameter) external returns (uint){
        return 1/parameter;
    } 
}
```

The transaction is reverted to the initial state.

### Mitigation
Add a check (require or revert) that the parameter granularity is not 0 at the begining of the function. 

Example for reserves function (apply to totalSupplyAvg too)

```diff
function reserves(uint granularity) external view returns(uint, uint) {
+	require(granularity != 0,"Division by 0");
    (uint[] memory _reserves0, uint[] memory _reserves1)= sampleReserves(granularity, 1);
    uint reserveAverageCumulative0;
    uint reserveAverageCumulative1;

    for (uint i = 0; i < _reserves0.length; ++i) {
        reserveAverageCumulative0 += _reserves0[i]; //normalize the reserves for TWAP LP Oracle pricing, 
        reserveAverageCumulative1 += _reserves1[i]; //
    }

    return (reserveAverageCumulative0 / granularity, reserveAverageCumulative1 / granularity);
}
```

### Lines in the code
[BaseV1-core.sol#L224-L235](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L224-L235)
[BaseV1-core.sol#L260-L269](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L260-L269)

# Non Critical
## [NC01] Use of block timestamp
### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), 
locking funds for periods of time, and various state-changing conditional statements that are time-dependent. 
Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Recommendation
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor 
(either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, 
or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; 
with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change 
a contract state can be more secure, as miners are unable to easily manipulate the block number.

### Lines in the code
[BaseV1-core.sol#L138](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L138)
[BaseV1-core.sol#L147](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L147)
[BaseV1-core.sol#L250](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L250)
[BaseV1-core.sol#L283](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L283)

## [NC02] Unnecesary use of variable
### Description
In the following function getUnderlyingPrice if the token is cCANTO then return directly getPriceNote but before the
return is asigning the value of address(wcanto) to underlying variable when it's not going to be used.

To mitigate it there are two choices:
1. Remove the assigned line as the example below
2. Use de value of uderlying in the getPriceNote call


```diff
function getUnderlyingPrice(CToken ctoken) external override view returns(uint) {
     address underlying;
    { //manual scope to pop symbol off of stack
    string memory symbol = ctoken.symbol();
    if (compareStrings(symbol, "cCANTO")) {
-       underlying = address(wcanto);
        return getPriceNote(address(wcanto), false);
    } else {
        underlying = address(ICErc20(address(ctoken)).underlying()); // We are getting the price for a CErc20 lending market
    }
    //set price statically to 1 when the Comptroller is retrieving Price
    if (compareStrings(symbol, "cNOTE")) { // note in terms of note will always be 1 
        return 1e18; // Stable coins supported by the lending market are instantiated by governance and their price will always be 1 note
    } 
    else if (compareStrings(symbol, "cUSDT") && (msg.sender == Comptroller )) {
        uint decimals = erc20(underlying).decimals();
        return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller
    } 
    else if (compareStrings(symbol, "cUSDC") && (msg.sender == Comptroller)) {
        uint decimals = erc20(underlying).decimals();
        return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller
    }
    }
    
    if (isPair(underlying)) { // this is an LP Token
        return getPriceLP(IBaseV1Pair(underlying));
    }
    // this is not an LP Token
    else {
        if (isStable[underlying]) {
            return getPriceNote(underlying, true); // value has already been scaled
        }

        return getPriceCanto(underlying) * getPriceNote(address(wcanto), false) / 1e18;
    }   
}
```
### Lines in the code
[BaseV1-periphery.sol#L492](https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L492)

