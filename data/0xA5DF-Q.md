## no option to unset `isStable`, in case it was set by mistake
`BaseV1Router01.isStable[someTokenAddress]` can only be set to true, in case the wrong token was set - there's no way to reverse it.
Consider allowing to set it to false too by modifying the `setStable()` function:

```solidity
    function setStable(address underlying, bool val) external returns (uint) {
        if (msg.sender != admin) {
            revert SenderNotAdmin(msg.sender, admin);
        }

        isStable[underlying] = val;
    }

```

## Identify known tokens by address rather than by symbol
Attacker can create a malicious token with the same symbol as the known tokens, therefore it'll be more secure to identify the known tokens (cCANTO, cNOTE, cUSDT, cUSDC) at `getUnderlyingPrice()` by their address (storing them as immutable vars) rather than by their symbol.
Even though this is not going to affect legitimate lending markets, attacker can use this to try convince victims his token (or lending market created with this token) is real.

## Unused code

The internal function `BaseV1Router01._returnStableBooleans` isn't used and can be removed

## `getPriceLP()` assumes all pairs are created with NOTE/CANTO
Filing this is QA since it seems from the docs that CANTO indeed doesn't intend to create other kinds of pairs
However, it is worth verifying that in order to not cause errors for other users who may be using this oracle, or in case a stable pair was mistakenly created with CANTO or vice versa.
Simply require that if `token0` isn't note/canto then the `token1` should be, and revert otherwise.