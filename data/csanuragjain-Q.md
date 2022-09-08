## User with first Mint lose funds

Contract:
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L305

Issue:
The very first mint (_totalSupply=0) will deduct MINIMUM_LIQUIDITY from user deposited amount. Hence the user will be in loss, however the amount is very less thus marking this as QA

Recommendation:
While initializing the contract, mint a small amount so that owner deploying the contract will pay for Minimum liquidity instead of user

## No provision for emergency migration

Contract:
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol

Issue:
If a security issue is identified in the pair contract, then there seems no way to instantly migrate liquidity funds from existing pair to new one