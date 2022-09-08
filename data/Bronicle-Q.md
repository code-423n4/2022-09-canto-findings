# Low issue

## Multiplication after division

1. **Summary**: Performing a multiplication after a division should be avoided as precision is lost in the division step.
2. **Details**: In order to minimize rounding errors, when performing mathematical operations, it is encouraged to leave the division step at the end of the expression.
3. **Github Permalinks**: https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L549-L593
4. **Mitigation**:

    The current implementation of the computation at [lines 581 to 585](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L581-L585) can be mathematically written as follows:

    $$\text{LpPricesCumulative} = \sum_i \frac{\left(\text{token0TVL}+\text{token1TVL}\right)\cdot 10^{18}}{\text{supply}_i}$$    

    which is equivalent to

    $$\text{LpPricesCumulative} = \sum_i \frac{\left(\text{assetReserves}_i\cdot\frac{\text{prices}_i}{\text{decimals}}+\text{token1TVL}\right)\cdot 10^{18}}{\text{supply}_i}$$

    Note that we can extract $10^{18}$ from the summation because it is constant. Moreover, if multiplying both the denominator and the numerator by $\text{decimals}$, we can do the same. This results in:

    $$\text{LpPricesCumulative} = \frac{10^{18}}{\text{decimals}} \sum_i \frac{\text{assetReserves}_i\cdot\text{prices}_i+\text{token1TVL}\cdot\text{decimals}}{\text{supply}_i}$$

    Moreover, if one checks that the number of decimals (let's call it $z$, so that $\text{decimals} = 10^{z}$) is less or equal than $18$, the above expression can be simplified to:

    $$\text{LpPricesCumulative} = 10^{18 - z} \sum_i \frac{\text{assetReserves}_i\cdot\text{prices}_i+\text{token1TVL}\cdot\text{decimals}}{\text{supply}_i}$$

    With this expression, one can reduce the amount of divisions performed and will most likely have a better rounding precision.

    Moreover, at [line 592](https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-periphery.sol#L592), more simplifications can be done. The expression

    $$\text{LpPrice}\cdot\frac{\text{getPriceNote(address(wcanto), false)}}{10^{18}}$$

    can be rewritten as

    $$\frac{\text{LpPricesCumulative}}{8}\cdot\frac{\text{getPriceNote(address(wcanto), false)}}{10^{18}}$$

    which in turn equals

    $$\frac{10^{18 - z}}{8} \sum_i \frac{\text{assetReserves}_i\cdot\text{prices}_i+\text{token1TVL}\cdot\text{decimals}}{\text{supply}_i}\cdot\frac{\text{getPriceNote(address(wcanto), false)}}{10^{18}}$$

    having thus
    
    $$\frac{1}{8\cdot 10^´z} \sum_i \frac{\text{assetReserves}_i\cdot\text{prices}_i+\text{token1TVL}\cdot\text{decimals}}{\text{supply}_i}\cdot\text{getPriceNote(address(wcanto), false)}$$

    which might be a better implementation of the computations.