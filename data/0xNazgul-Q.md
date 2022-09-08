## [NAZ-N] Line Length
**Severity**: Informational
**Context**: [`BaseV1-core.sol#L143`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L143), [`BaseV1-core.sol#L147`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L147), [`BaseV1-core.sol#L149`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L149), [`BaseV1-core.sol#L251-L252`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L251-L252), [`BaseV1-core.sol#L284`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L284), [`BaseV1-periphery.sol#L495`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L495), [`BaseV1-periphery.sol#L499`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L499)

**Description**:
Max line length must be no more than 120 but many lines are extended past this length.

**Recommendation**:
Consider cutting down the line length below 120.


## [NAZ-N] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`BaseV1-periphery.sol#L448`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L448), [`BaseV1-periphery.sol#L525`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L525), [`BaseV1-periphery.sol#L537`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L537), [`BaseV1-periphery.sol#L549`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L549), [`BaseV1-periphery.sol#L579`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L579), [`BaseV1-periphery.sol#L586`](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L586)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Private variables and functions should lead with an `underscore`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the [Solidity style guide](https://docs.soliditylang.org/en/latest/style-guide.html). 


## [NAZ-N] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.


## [NAZ-N] Older Version Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap)

**Description**:
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs. 

**Recommendation**:
Consider using the most recent version.