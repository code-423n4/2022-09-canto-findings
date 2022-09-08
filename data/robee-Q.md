

## ETH send return value is ignored while is gas limited

The use of send() / call() to send ETH may have unintended outcomes on the eth being sent to the receiver. Eth may be irretrievable or undelivered if the msg.sender or feeRecipient is a smart contract. Funds can potentially be lost if;
        1. The smart contract fails to implement the payable fallback function
        2. The fallback function uses more than 2300 gas units 
Different from .transfer(...), .send(...) and call(...) doesn't revert when it fails, and therefore its retrun value is extremely important.
The latter situation may occur in the instance of gas cost changes. The impact would mean that any contracts receiving funds would potentially be unable to retrieve funds from the transaction.
A detailed explanation of why relying on payable().transfer() may result in unexpected loss of eth can be found here: https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now 
This is not just a best-practice advice since the return value isn't considered!!! If you would consider it then it was just a best practice.

### Code instances:

        CompoundLens.sol,  lines: 78,   91,  66,   124,             

        Of the form ```address(comptroller).call(...)```


## Unbounded loop on array can lead to DoS

The attacker can push unlimitedly to an array, that some function loop over this array.
If increasing the array size enough, calling the function that does a loop over the array will always revert since there is a gas limit.
This is an High Risk issue since those arrays are publicly allows to push items into them.

### Code instance:

        BaseV1-core.sol (L563): Unbounded loop on the array allPairs that can be publicly pushed by ['createPair']



## approve return value is ignored

Some tokens don't correctly implement the EIP20 standard and their approve function returns void instead of a success boolean. 
Calling these functions with the correct EIP20 function signatures will always revert.
Tokens that don't correctly implement the latest EIP20 spec, like USDT, will be unusable in the mentioned contracts as they revert the transaction because of the missing return value.
We recommend using OpenZeppelin’s SafeERC20 versions with the safeApprove function that handle the return value check as well as non-standard-compliant tokens.
The list of occurrences in format (solidity file, line number, actual line)
### Code instances:

         CDaiDelegate.sol, 56,         dai.approve(daiJoinAddress, type(uint).max);
        AccountantDelegate.sol, 38, note.approve(cnoteAddress_, type(uint).max); 



## Open TODOs

Open TODOs can hint at programming or architectural errors that still need to be fixed. 
These files has open TODOs:

### Code instances:

        Open TODO in Comptroller.sol line 1275 :         // TODO: Don't distribute supplier COMP if the user is not in the borrower market.

        Open TODO in Comptroller.sol line 1236 :         // TODO: Don't distribute supplier COMP if the user is not in the supplier market.




## Div by 0


Division by 0 can lead to accidentally revert,
(An example of a similar issue - https://github.com/code-423n4/2021-10-defiprotocol-findings/issues/84)

### Code instances:

        https://github.com/code-423n4/2022-09-canto/tree/main/src/deps/JumpRateModel.sol#L69 cash, borrows, reserves might be 0
        https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L582 decimals might be 0
        https://github.com/code-423n4/2022-09-canto/tree/main/src/deps/CToken.sol#L310 _totalSupply might be 0
        https://github.com/code-423n4/2022-09-canto/tree/main/src/deps/BaseJumpRateModelV2.sol#L132 kink_ might be 0
        https://github.com/code-423n4/2022-09-canto/tree/main/src/deps/BaseJumpRateModelV2.sol#L86 cash, borrows, reserves might be 0




## Add a timelock

To give more trust to users: functions that set key/critical variables should be put behind a timelock.

### Code instances:

        https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L560
        https://github.com/code-423n4/2022-09-canto/tree/main/src/deps/Treasury/TreasuryDelegator.sol#L30
        https://github.com/code-423n4/2022-09-canto/tree/main/src/deps/Timelock.sol#L37
        https://github.com/code-423n4/2022-09-canto/tree/main/src/deps/CNote.sol#L15
        https://github.com/code-423n4/2022-09-canto/tree/main/src/deps/Comptroller.sol#L1477
        https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol#L555
        https://github.com/code-423n4/2022-09-canto/tree/main/src/deps/Accountant/AccountantDelegator.sol#L41






## Not verified input


external / public functions parameters should be validated to make sure the address is not 0.
Otherwise if not given the right input it can mistakenly lead to loss of user funds.    

### Code instances:

        ComptrollerG7.sol.mintAllowed minter
        ComptrollerG7.sol._setBorrowCapGuardian newBorrowCapGuardian
        CToken.sol.transfer dst
        Comp.sol.constructor account



## Treasury may be address(0)


    Make sure the treasury is not address(0).
        
        
### Code instance:

        AccountantDelegate.sol.initialize treasury_



## Solidity compiler versions mismatch


The project is compiled with different versions of solidity, which is not recommended because it can lead to undefined behaviors.





## Loss Of Precision

This issue is about arithmetic computation that could have been done more percise. 
The following are places in the codebase in which you multiplied after the divisions. 
Doing the multiplications at start lead to more accurate calculations. 
This is a list of places in the code that this appears (Solidity file, line number, actual line): 

### Code instances:

        BaseV1-core.sol, 429, amountIn = tokenIn == token0 ? amountIn * 1e18 / decimals0 : amountIn * 1e18 / decimals1;
        BaseV1-core.sol, 386, return x0*(y*y/1e18*y/1e18)/1e18+(x0*x0/1e18*x0/1e18)*y/1e18;
        DAIInterestRateModelV3.sol, 85, return (pot.dsr() - RAY_BASE) / RAY_TO_BASE_SCALE * SECONDS_PER_BLOCK;
        DAIInterestRateModelV3.sol, 95, uint stabilityFeePerBlock = (duty + jug.base() - RAY_BASE) / RAY_TO_BASE_SCALE * SECONDS_PER_BLOCK; 



## safeApprove of openZeppelin is deprecated


You use safeApprove of openZeppelin although it's deprecated.
(see https://github.com/OpenZeppelin/openzeppelin-contracts/blob/566a774222707e424896c0c390a84dc3c13bdcb2/contracts/token/ERC20/utils/SafeERC20.sol#L38)
You should change it to increase/decrease Allowance as OpenZeppilin says.
        
### Code instances:

        Deprecated safeApprove in AccountantDelegate.sol line 38: note.approve(cnoteAddress_, type(uint).max);
        Deprecated safeApprove in CDaiDelegate.sol line 56: dai.approve(daiJoinAddress, type(uint).max);

## Not verified owner


        owner param should be validated to make sure the owner address is not address(0).
        Otherwise if not given the right input all only owner accessible functions will be unaccessible.
        
        
### Code instances:

        CToken.sol.balanceOfUnderlying owner
        CErc20Delegator.sol.balanceOfUnderlying owner
        JumpRateModelV2.sol.constructor owner_



## Named return issue

Users can mistakenly think that the return value is the named return, but it is actually the actualreturn statement that comes after. To know that the user needs to read the code and is confusing.
Furthermore, removing either the actual return or the named return will save gas. 

### Code instances:

        BaseV1-periphery.sol, getAmountOut
        BaseV1-periphery.sol, quoteRemoveLiquidity
        GovernorBravoDelegate.sol, getActions



## Two Steps Verification before Transferring Ownership

The following contracts have a function that allows them an admin to change it to a different address. If the admin accidentally uses an invalid address for which they do not have the private key, then the system gets locked.
It is important to have two steps admin change where the first is announcing a pending new admin and the new address should then claim its ownership. 
A similar issue was reported in a previous contest and was assigned a severity of medium: [code-423n4/2021-06-realitycards-findings#105](https://github.com/code-423n4/2021-06-realitycards-findings/issues/105) 

### Code instances:

        NoteInterest.sol
        BaseV1-core.sol
        BaseV1-periphery.sol



## Assert instead require to validate user inputs


        From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix.
        With assert the user pays the gas and with require it doesn't. The ETH network gas isn't cheap and users can see it as a scam.
        
### Code instances:

        ComptrollerG7.sol : reachable assert in line 203
        Comptroller.sol : reachable assert in line 363
        BaseV1-periphery.sol : reachable assert in line 427
        BaseV1-periphery.sol : reachable assert in line 84
        ComptrollerG7.sol : reachable assert in line 349
        BaseV1-periphery.sol : reachable assert in line 281
        Comptroller.sol : reachable assert in line 217
        BaseV1-periphery.sol : reachable assert in line 235



## In the following public update functions no value is returned

In the following functions no value is returned, due to which by default value of return will be 0. 
We assumed that after the update you return the latest new value. 
(similar issue here: https://github.com/code-423n4/2021-10-badgerdao-findings/issues/85). 

### Code instances:

        Comptroller.sol, updateContributorRewards
        NoteInterest.sol, updateBaseRate
        NoteInterest.sol, _setUpdateFrequency
        BaseJumpRateModelV2.sol, updateJumpRateModel
        ComptrollerG7.sol, updateContributorRewards


## Require with empty message

The following requires are with empty messages. 
This is very important to add a message for any require. So the user has enough information to know the reason of failure.
### Code instances:

        Solidity file: BaseV1-core.sol, In line 561 with Empty Require message.
        Solidity file: BaseV1-core.sol, In line 562 with Empty Require message.
        Solidity file: BaseV1-core.sol, In line 581 with Empty Require message.
        Solidity file: NoteInterest.sol, In line 102 with Empty Require message.
        Solidity file: Note.sol, In line 23 with Empty Require message.