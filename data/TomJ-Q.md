## Table of Contents
### Low Risk Issues
- Performing a Multiplication on the Result of a Division
### Non-critical Issues
- Define Magic Numbers to Constant

## Low Risk Issues
### Performing a Multiplication on the Result of a Division

#### Issue
Solidity integer division might truncate. As a result, performing multiplication before division can sometimes avoid loss of precision.
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply

#### PoC
1. Line 582 of getPriceLP() of BaseV1-periphery.sol 
```solidity
            uint token0TVL = assetReserves[i] * (prices[i] / decimals);
```
https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L582

#### Mitigation
Consider ordering multiplication before division.

&ensp;
## Non-critical Issues
### Define Magic Numbers to Constant

#### Issue
It is best practice to define magic numbers to constant rather than just using as a magic number.
This improves code readability and maintainability. 

#### PoC
1. Magic number: 10
```solidity
./BaseV1-periphery.sol:18:            return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller
./BaseV1-periphery.sol:22:            return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller
./BaseV1-periphery.sol:46:        uint decimals = 10 ** token.decimals(); // get decimals of token
./BaseV1-periphery.sol:58:        uint decimals = 10 ** token.decimals();
./BaseV1-periphery.sol:75:                decimals = 10 ** (erc20(token1).decimals()); // we must normalize the price of token1 to 18 decimals
./BaseV1-periphery.sol:79:                decimals = 10 ** (erc20(token0).decimals());
./BaseV1-periphery.sol:85:                decimals = 10 ** (erc20(token1).decimals());
./BaseV1-periphery.sol:89:                decimals = 10 ** (erc20(token0)).decimals();
```

2. Magic number: 1e18
```solidity
./BaseV1-periphery.sol:18:            return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller
./BaseV1-periphery.sol:22:            return 1e18 * 1e18 / (10 ** decimals); //Scale Price as a mantissa to maintain precision in comptroller
```

#### Mitigation
Define magic numbers to constant.

&ensp;