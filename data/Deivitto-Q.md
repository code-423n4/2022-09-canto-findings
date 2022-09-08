# QA
# Low
## Prevent div by 0
### Impact
Division by 0 will revert code, however on some locations this isn’t checked.

### Proof of Concept
Navigate to the following contracts,

`granularity` parameter is used with no validation of being > 0, if the variable is equal to zero, div by zero will occur.
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L234
        return (reserveAverageCumulative0 / granularity, reserveAverageCumulative1 / granularity);


https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L268
        return (totalSupplyCumulativeAvg / granularity);


Different calls to `erc20(address).decimals()` are used with no validation of being > 0, therefore, in the edge case of an erc20 that overrides decimals() to be 0, `decimals` variable will be equal to zero therefore div by zero will occur.

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L533
        return price * 1e18 / decimals; //return the scaled price

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L545
        return price * 1e18 / decimals; // divide by decimals now to maintain precision

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L582
            uint token0TVL = assetReserves[i] * (prices[i] / decimals);


`supply[i]` is equal to 0, div by zero will occur.
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L584
            LpPricesCumulative += (token0TVL + token1TVL) * 1e18 / supply[i];

### Recommended Mitigation Steps
Recommend making sure division by 0 won’t occur by checking the variables beforehand and handling this edge cases.




## `block.timestamp` used as time proxy 
### Summary:
Risk of using block.timestamp for time should be considered. 
### Details:
block.timestamp is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L138
        uint blockTimestamp = block.timestamp;


### Mitigation
Consider using an oracle for precision
Consider the risk of using block.timestamp as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 


## Missing event for core paths/functions/parameters
### Summary:
Events are important for critical parameter change and some paths of the code where ether/tokens are involved.

### Github Permalinks
No events are emitted in `BaseV1-periphery.sol`, however, functions with ether involved change of permissions (for example: admin)
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol

### Recommended Mitigation Steps
Add events for better monitoring of the code



# Informational
##  Use of magic numbers is confusing and risky
### Summary:
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
### Details:
Values are hardcoded and would be more readable and maintainable if declared as a constant

In several locations in the code numbers like 1e18, 8, 500, 1e18 are used. It quite easy to make a mistake somewhere, also when comparing values or missing a 0 what can lead to some bad behaviors.]

### Github Permalinks
1e187
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L499-L508
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L545
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L584
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L592

8
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L532
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L544
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L550
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L558-L577


2
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L177


### Mitigation
Replace magic hardcoded numbers with declared constants.
Define constants for the numbers used throughout the code.
Comment what these numbers are intended for


## Missing Natspec 
 ### Summary: 
 Missing Natspec and regular comments affect readability and maintainability of a codebase. 
 ### Details: 
 Contracts has a few inline comments but mostly it has lack of comments and full lack of any natspec property
 ### Github Permalinks 
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol

 ### mitigation
 Add @param descriptors
 Add @return descriptors
 Add Natspec comments. 
 Add inline comments. 
 Add comments for what the contract does


## Inconsistent spacing in comments
Some lines use // x and some use //x. The instances below point out the usages that don't follow the majority, within each file
### Details
`totalSupplyCumulativeLast += _totalSupply * timeElapsed; //update totalSupply after each change in LP Token supply`
But following the style of the other comments would be:
`totalSupplyCumulativeLast += _totalSupply * timeElapsed; // update totalSupply after each change in LP Token supply`
### Github Permalinks
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L143
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L230
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L265
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L486
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L489
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L497
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L503
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L507
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L524
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L533
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L559

## Comment without content
Some lines has the start of a comment but without any kind of comment
### Github Permalinks
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L231
            reserveAverageCumulative1 += _reserves1[i]; //
### Recommendation
Add the expected comment or remove in case there is nothing to say.





## Maximum line length exceeded
### Summary:
 Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details: 
Lines that exceed the 79 (or 99) character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
### Github Permalinks: 
https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L137

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L143

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L147

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L149

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L230

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L237

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L251

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L252

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L265

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-core.sol#L284

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L495

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L499

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L503

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L507

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L532

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L536

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L539

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L559

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L560

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L569

https://github.com/code-423n4/2022-09-canto/blob/a6406d21b5c4359ec3fd2139a2959d3ecbf35a54/src/Swap/BaseV1-periphery.sol#L592
### Mitigation
Reduce line length to less than 99 at least to improve maintainability and readability of the code 




