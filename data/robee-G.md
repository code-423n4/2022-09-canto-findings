
## Unnecessary array boundaries check when loading an array element twice


    There are places in the code (especially in for-each loops) that loads the same array element more than once. 
    In such cases, only one array boundaries check should take place, and the rest are unnecessary.
    Therefore, this array element should be cached in a local variable and then be loaded
    again using this local variable, skipping the redundant second array boundaries check: 
    
### Code instances:

        BaseV1-periphery.sol.getAmountsOut - double load of routes[i]
        Comptroller.sol.claimComp - double load of holders[j]
        BaseV1-periphery.sol._swap - double load of routes[i]


## Storage double reading. Could save SLOAD

Reading a storage variable is gas costly (SLOAD). In cases of multiple read of a storage variable in the same scope, caching the first read (i.e saving as a local variable) can save gas and decrease the
 overall gas uses. The following is a list of functions and the storage variables that you read twice: 

### Code instances:

        NoteInterest.sol: admin is read twice in _setBaseRatePerYear
        NoteInterest.sol: admin is read twice in _setAdjusterCoefficient
        NoteInterest.sol: baseRatePerYear is read twice in updateBaseRate
        BaseJumpRateModelV2.sol: owner is read twice in updateJumpRateModel
        JumpRateModel.sol: multiplierPerBlock is read twice in getBorrowRate
        NoteInterest.sol: BASE is read twice in updateBaseRate
        NoteInterest.sol: admin is read twice in _setUpdateFrequency



## Unnecessary default assignment


Unnecessary default assignments, you can just declare and it will save gas and have the same meaning.
    

### Code instances:

        Comptroller.sol (L#84) : uint internal constant closeFactorMaxMantissa = 0.9e18; 
        ComptrollerG7.sol (L#72) : uint internal constant closeFactorMinMantissa = 0.05e18; 
        ComptrollerG7.sol (L#75) : uint internal constant closeFactorMaxMantissa = 0.9e18; 
        ErrorReporter.sol (L#75) : uint public constant NO_ERROR = 0; 
        Timelock.sol (L#17) : uint public constant MINIMUM_DELAY = 0 days;
        Comptroller.sol (L#81) : uint internal constant closeFactorMinMantissa = 0.05e18; 
        ComptrollerG7.sol (L#78) : uint internal constant collateralFactorMaxMantissa = 0.9e18; 
        Comptroller.sol (L#87) : uint internal constant collateralFactorMaxMantissa = 0.9e18; 



## State variables that could be set immutable

In the following files there are state variables that could be set immutable to save gas. 

### Code instances:

        multiplierPerBlock in JumpRateModel.sol
        _symbol in ERC20.sol
        baseRatePerBlock in JumpRateModel.sol
        jumpMultiplierPerBlock in JumpRateModel.sol
        _symbol in WETH.sol


## More efficient Struct packing of Market in the contract ComptrollerStorage.sol


        The following structs could change the order of their stored elements to decrease memory uses.
        and number of occupied slots. Therefore will save gas at every store and load from memory.
        
In ComptrollerStorage.sol, Market is optimized to: 3 slots from: 4 slots.
The new order of types (you choose the actual variables):
        1. uint
        2. mapping
        3. bool
        4. bool



## More efficient Struct packing of Proposal in the contract GovernorBravoInterfaces.sol


        The following structs could change the order of their stored elements to decrease memory uses.
        and number of occupied slots. Therefore will save gas at every store and load from memory.
        
In GovernorBravoInterfaces.sol, Proposal is optimized to: 7 slots from: 8 slots.
The new order of types (you choose the actual variables):
        1. uint
        2. uint
        3. address[]
        4. uint[]
        5. string[]
        6. bytes[]
        7. address
        8. bool
        9. bool


## Unnecessary equals boolean


Boolean variables can be checked within conditionals directly without the use of equality operators to true/false.

### Code instances:

        Comptroller.sol, 1068: require(msg.sender == admin || state == true, "only admin can unpause");
        Comptroller.sol, 1355: if (borrowers == true) {
        ComptrollerG7.sol, 1244: if (suppliers == true) {
        ComptrollerG7.sol, 1030: require(msg.sender == admin || state == true, "only admin can unpause");
        Comptroller.sol, 1461: borrowGuardianPaused[address(cToken)] == true &&
        Comptroller.sol, 1058: require(msg.sender == admin || state == true, "only admin can unpause");


## Use bytes32 instead of string to save gas whenever possible


    Use bytes32 instead of string to save gas whenever possible.
    String is a dynamic data structure and therefore is more gas consuming then bytes32.

    
### Code instances:

        Comp.sol (L6), string public constant name = "Compound"; 
        Comp.sol (L9), string public constant symbol = "COMP"; 
        GovernorBravoDelegate.sol (L9), string public constant name = "Compound Governor Bravo"; 
        GovernorAlpha.sol (L6), string public constant name = "Compound Governor Alpha"; 

## Use != 0 instead of > 0


Using != 0 is slightly cheaper than > 0. (see https://github.com/code-423n4/2021-12-maple-findings/issues/75 for similar issue)


### Code instances:

        BaseV1-periphery.sol, 114: change 'reserveA > 0' to 'reserveA != 0'
        WETH.sol, 29: change 'balance > 0' to 'balance != 0'
        ERC20.sol, 216: change 'balance > 0' to 'balance != 0'
        WETH.sol, 69: change 'balance > 0' to 'balance != 0'
        BaseV1-periphery.sol, 113: change 'amountA > 0' to 'amountA != 0'
        CNote.sol, 257: change 'redeemTokensIn > 0' to 'redeemTokensIn != 0'
        ComptrollerG7.sol, 319: change 'redeemAmount > 0' to 'redeemAmount != 0'
        Comp.sol, 245: change 'amount > 0' to 'amount != 0'
        ERC20.sol, 255: change 'balance > 0' to 'balance != 0'
        Comptroller.sol, 333: change 'redeemAmount > 0' to 'redeemAmount != 0'
        Comp.sol, 265: change 'nCheckpoints > 0' to 'nCheckpoints != 0'
        CNote.sol, 256: change 'redeemTokensIn > 0' to 'redeemTokensIn != 0'
        ComptrollerG7.sol, 1264: change 'amount > 0' to 'amount != 0'
        Comptroller.sol, 1384: change 'amount > 0' to 'amount != 0'
        AccountantDelegate.sol, 118: change 'balance > 0' to 'balance != 0'
        CToken.sol, 766: change 'balance > 0' to 'balance != 0'
        CToken.sol, 488: change 'redeemTokensIn > 0' to 'redeemTokensIn != 0'
        CToken.sol, 489: change 'redeemTokensIn > 0' to 'redeemTokensIn != 0'
        BaseV1-periphery.sol, 114: change 'reserveB > 0' to 'reserveB != 0'
        SafeMath.sol, 154: change 'b > 0' to 'b != 0'



## Empty else statement can be removed to save gas


        Empty else statement can be removed to save gas.
        
    
### Code instance:

        Maximillion.sol.repayBehalfExplicit



## Unnecessary index init


In for loops you initialize the index to start from 0, but it already initialized to 0 in default and this assignment cost gas. 
It is more clear and gas efficient to declare without assigning 0 and will have the same meaning:

### Code instances:

        CompoundLens.sol, 371
        Comptroller.sol, 1418
        BaseV1-periphery.sol, 145
        GovernorBravoDelegate.sol, 88

## Consider inline the following functions to save gas


    You can inline the following functions instead of writing a specific function to save gas.
    (see https://github.com/code-423n4/2021-11-nested-findings/issues/167 for a similar issue.)

    
### Code instances

        ExponentialNoError.sol, add_, { return a + b; }
        ExponentialNoError.sol, mul_, { return a * b; }
        ExponentialNoError.sol, div_, { return a / b; }
        ExponentialNoError.sol, sub_, { return a - b; }



## Inline one time use functions


The following functions are used exactly once. Therefore you can inline them and save gas and improve code clearness.
    

### Code instances:

        WETH.sol, _approve
        Comptroller.sol, _initializeMarket
        Note.sol, _mint_to_Accountant
        BaseJumpRateModelV2.sol, getBorrowRateInternal
        CToken.sol, liquidateBorrowFresh
        CToken.sol, _setReserveFactorFresh
        ComptrollerG7.sol, setCompSpeedInternal
        ComptrollerG7.sol, _addMarketInternal
        Comp.sol, safe32
        CompoundLens.sol, add
        CToken.sol, borrowFresh
        SafeMath.sol, mul
        Comptroller.sol, getAccountLiquidityInternal
        GovernorBravoDelegate.sol, queueOrRevertInternal
        Comptroller.sol, setCompSpeedInternal
        ComptrollerG7.sol, getAccountLiquidityInternal
        CompoundLens.sol, setBravoProposal
        Comp.sol, getChainId
        CompoundLens.sol, sub
        ERC20.sol, _spendAllowance
        SimplePriceOracle.sol, compareStrings
        Comptroller.sol, _addMarketInternal
        CToken.sol, _reduceReservesFresh
        CToken.sol, _addReservesFresh
        CompoundLens.sol, setProposal
        SafeMath.sol, add
        CToken.sol, mintFresh
        CompoundLens.sol, getCompSpeeds



## Cache powers of 10 used several times

You calculate the power of 10 every time you use it instead of caching it once as a constant variable and using it instead. 
Fix the following code lines: 

### Code instances:

BaseV1-core.sol, 93 : You should cache the used power of 10 as constant state variable since it's used several times (2):          decimals1 = 10**erc20(_token1).decimals();

BaseV1-core.sol, 92 : You should cache the used power of 10 as constant state variable since it's used several times (2):          decimals0 = 10**erc20(_token0).decimals();




## Change if -> revert pattern to require

Change if -> revert pattern to 'require' to save gas and improve code quality,
if (some_condition) { 
        revert(revert_message) 
}

to: require(!some_condition, revert_message)

In the following locations:

### Code instances:

        Comptroller.sol, 334
        ComptrollerG7.sol, 320



## Gas Optimization On The 2^256-1


Some projects (e.g. Uniswap - https://github.com/Uniswap/interface/blob/main/src/hooks/useApproveCallback.ts#L88)
set the default value of the user's allowance to 2^256 - 1. Since the value 2^256 - 1 can also be represented in 
hex as 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff. From Ethereum's yellow paper we know 
that zeros are cheaper than non-zero values in the hex representation. Considering this fact, an alternative 
choice could be now 0x8000000000000000000000000000000000000000000000000000000000000000 or 2^255 to represent 
"infinity". If you do the calculations with Remix, you will see that the former costs 47'872 gas, while the latter 
costs 45'888 gas. If you accept that infinity can also be represented via 2^255 (instead of 2^256-1), which almost 
all projects can - you can already save about 4% gas leveraging this optimisation trick on those calculations.
     
    
### Code instance:

        ERC20.sol (L#20): uint256 MAX_INT = 2**256-1; )



## Do not cache msg.sender


We recommend not to cache msg.sender since calling it is 2 gas while reading a variable is more.


### Code instances:

        https://github.com/code-423n4/2022-09-canto/tree/main/src/deps/ERC20.sol#L145




## Short the following require messages

The following require messages are of length more than 32 and we think are short enough to short
them into exactly 32 characters such that it will be placed in one slot of memory and the require 
function will cost less gas. 
The list: 

### Code instances:

        Solidity file: SafeMath.sol, In line 98, Require message length to shorten: 33, The message: SafeMath: multiplication overflow
        Solidity file: BaseV1-periphery.sol, In line 113, Require message length to shorten: 33, The message: BaseV1Router: INSUFFICIENT_AMOUNT
        Solidity file: Comptroller.sol, In line 1100, Require message length to shorten: 33, The message: Only admin can call this function
        Solidity file: CToken.sol, In line 34, Require message length to shorten: 35, The message: market may only be initialized once
        Solidity file: BaseV1-periphery.sol, In line 305, Require message length to shorten: 35, The message: BaseV1Router: INSUFFICIENT_B_AMOUNT





## Prefix increments are cheaper than postfix increments

Prefix increments are cheaper than postfix increments. 
Further more, using unchecked {++x} is even more gas efficient, and the gas saving accumulates every iteration and can make a real change
There is no risk of overflow caused by increamenting the iteration index in for loops (the `++i` in `for (uint256 i = 0; i < numIterations; ++i)`).
But increments perform overflow checks that are not necessary in this case.
These functions use not using prefix increments (`++x`) or not using the unchecked keyword: 

### Code instances:

        just change to unchecked: Comptroller.sol, i, 1418
        change to prefix increment and unchecked: Comptroller.sol, i, 1352
        just change to unchecked: Comptroller.sol, i, 1111
        change to prefix increment and unchecked: ComptrollerG7.sol, i, 942
        change to prefix increment and unchecked: BaseV1-periphery.sol, i, 145



## Caching array length can save gas


Caching the array length is more gas efficient.
This is because access to a local variable in solidity is more efficient than query storage / calldata / memory.
We recommend to change from:    

    for (uint256 i=0; i<array.length; i++) { ... }

to: 

    uint len = array.length  
    for (uint256 i=0; i<len; i++) { ... }


### Code instances:

        ComptrollerG7.sol, assets, 719
        GovernorBravoDelegate.sol, targets.newProposal, 66
        Comptroller.sol, allMarkets, 964
        Comptroller.sol, holders, 1369
        BaseV1-periphery.sol, routes, 145



## Unused state variables

Unused state variables are gas consuming at deployment (since they are located in storage) and are 
a bad code practice. Removing those variables will decrease deployment gas cost and improve code quality. 
This is a full list of all the unused storage variables we found in your code base. 

### Code instances:

        ExponentialNoError.sol, halfExpScale
        ComptrollerInterface.sol, isComptroller
        ComptrollerG7.sol, closeFactorMinMantissa
        ComptrollerG7.sol, collateralFactorMaxMantissa
        InterestRateModel.sol, isInterestRateModel

## Unused declared local variables

Unused local variables are gas consuming, since the initial value assignment costs gas. And are 
a bad code practice. Removing those variables will decrease the gas cost and improve code quality. 
This is a full list of all the unused storage variables we found in your code base. 

### Code instances:

        Comptroller.sol, updateCompSupplyIndex, compAccrued
        ComptrollerG7.sol, distributeSupplierComp, supplyState
        ComptrollerG7.sol, updateCompSupplyIndex, compAccrued
        ComptrollerG7.sol, updateCompBorrowIndex, borrowAmount
        ComptrollerG7.sol, updateCompSupplyIndex, supplyTokens