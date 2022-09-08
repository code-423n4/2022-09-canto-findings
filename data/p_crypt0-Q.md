
# BaseV1-Periphery.sol

## getPriceNote()

#### [Low] Potential loss of precision in return statement

````
return price * 1e18 / decimals;
````

Best practice would be:

````
return (price * 1e18) / decimals;
````


## getUnderlyingPrice()

#### [Informational] Missing comments to describe parameters:

Add comments for function:
````
/**
	@notice Returns the underlying price of the assets as a mantissa (scaled by 1e18)
	@param cToken the underlying asset whose value in Canto or stable terms, or part of a canto and note pool will be returned.
	@dev only comptroller approved cTokens can be returned.
**/
````

##### [Low] Potential loss of precision in return statement:

Do multiplication before division  - best practice would be to wrap division in parentheses.

````
return getPriceCanto(underlying) * getPriceNote(address(wcanto), false) / 1e18;
````

Best practice:

````
return (getPriceCanto(underlying) * getPriceNote(address(wcanto), false)) / 1e18;
````

## getPriceCanto()

#### [Informational] Missing comments to describe input parameters
/**
	@notice Gets the price for asset in terms of Canto.
	@param token_ the token to be priced in Canto.
	@return price the scaled price of token_ in Canto.
**/

## getPriceNote()

#### [Informational] Missing comments to describe input parameters

/**
	@notice Gets the price for asset in terms of Note.
	@param token_ the token to be priced in Note.
	@param stable whether the token is a stable asset (i.e. 1 token_ = 1 Note).
	@return price the scaled price of the asset in Note.
**/

# BaseV1-Core.sol

## _update()
#### [informational] Missing comments to describe input parameters

/**
	@notice Updates reserves and, on the first call per block, price accumulators.
	@param balance0 the current balance to be added to reserve.
	@param balance1 the current balance to be added to reserve.
	@param reserve0 the current reserve.
	@param reserve1 the current reserve.
	@param totalSupply the current total supply.
	
**/

## reserves()
#### [Informational] Missing comments to describe input parameters and function of method.

/**
	@notice Gets the total sum of reserves for each pair.
	@param granularity the size of the _reserves arrays when taking a sample of the reserves using sampleReserves(uint, uint).
	@return (reserveAverageCumulative0 / granularity, reserveAverageCumulative1 / granularity) the mean average for the reserve, given a granularity.
**/

## sampleReserves()

#### [Informational] Missing comments to describe input parameters and function of method.

/**
	@notice Gets the time-weighted value for each reserve for a point and window specified by the user.
	@param points the point to take the window from
	@param window the size of the time-window to average prices over.
	@return the time-weighted value for _reserve0 and _reserve1
**/

## totalSupplyAvg()

#### [Informational] Missing comments to describe input parameters and function of method.

/**
	@notice Gets the total supply over granularity.
	@param granularity the grain to measure the total supply with.
	@return (totalSupplyCumulativeAvg / granularity) the total supply divided by the sample supply dictated by granularity.
**/

## sampleSupply()

#### [Informational] Missing comments to describe input parameters and function of method.

/** 
	@notice Gets the time-weighted value for reserves between observation points.
	@param points the point to start observation at.
	@param window the size to observe from point.
	@return _totalSupply the time-weighted average of the total supply from point over a window.
**/ 
