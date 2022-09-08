#1 Missing natspec comment in basev1Pair and baseV1Factory

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L99-L610

A function has a natspec comment to explain utility about function or parameter. So add natspec comment to increase readability

#2 Mint(): missing zero address check

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L294

To avoid zero address. We suggest to add simple check zero address in the function.

#3 Burn(): missing zero address check 

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L319

To avoid zero address. We suggest to add simple check zero address in the function.

#4 Skim(): missing zero address check 

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L375

To avoid zero address. We suggest to add simple check zero address in the function.

#5 Swap(): missing zero address check 

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L341

To avoid zero address. We suggest to add simple check zero address in the function.

#6 _mint(): Missing zero check address

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L451

To avoid zero address. We suggest to add simple check zero address in the function. check openzepellin docs

#7 _burn: Missing check zero address and account must have amount.

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L457

To avoid zero address, we suggest to add simple check zero address in the function. 
And add check requirement amount must be greater than zero to ensure theaccount have some amount
check openzeppelin docs 

#8 Use safeapprove or safeIncraseAllowance

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L463

 the approve() function returns a boolean indicating whether it was successful or not. Best practice is to either check the return value or use safeApprove() / safeIncreaseAllowance() which will revert if the operation was unsuccessful. we suggest to use safeApprove() / safeIncreaseAllowance() instead of approve()

#9 Missing emit transfer

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L495

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L500

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L522
These function() have an event but miss the emit, to increase credibility we suggest to add transfer emit.

#10. Missing indexed field of pair

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L546

event is missing indexed fields. Add indexed at pair.

#11 Unused event

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L76

event not used. we suggest to remove when event not use anymore.

#12 Missing check constructor address

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-periphery.sol#L75

constructor have params address,  to avoid zero address. we suggest to consider add simple check address(0) to the constructor

#13 Missing natspec comment in BaseV1Router01

https://github.com/code-423n4/2022-09-canto/blob/65fbb8b9de22cf8f8f3d742b38b4be41ee35c468/src/Swap/BaseV1-core.sol#L99-L610

A function has a natspec comment to explain utility about function or parameter. So add natspec comment to increase readability