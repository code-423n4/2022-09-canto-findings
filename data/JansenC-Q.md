# Title
"Fixed" Swap

# Impacts
It's best to give to the user the option to choose the liquidity, and not constrain it. Once, in some circumstance, it can lead to unavailability of the service.

# Code
https://github.com/code-423n4/2022-09-canto/blob/6f98ffa7ec55c2ca03aa4cd2301503487f0c412d/src/Swap/BaseV1-core.sol#L329
https://github.com/code-423n4/2022-09-canto/blob/6f98ffa7ec55c2ca03aa4cd2301503487f0c412d/src/Swap/BaseV1-core.sol#L343

# Recommendation
Let the user decide what liquidity he is comfortable with.

# Lack of zero-address check

#Impact
It is possible to set an empty address as admin by mistake.

# Code
https://github.com/code-423n4/2022-09-canto/blob/6f98ffa7ec55c2ca03aa4cd2301503487f0c412d/src/Swap/BaseV1-core.sol#L557
https://github.com/code-423n4/2022-09-canto/blob/6f98ffa7ec55c2ca03aa4cd2301503487f0c412d/src/Swap/BaseV1-core.sol#L577