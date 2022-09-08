1. 

Contract: BaseV1-core.sol

It is unnecessary to initialize variables in for loops as they get set to 0 by default in:

	line 229
	line 264
	
Recommendation:

	 for (uint i; i < _reserves0.length; ++i) {
	 for (uint i; i < _totalSupplyAvg.length; ++i) {
	
2.

Contract: BaseV1-core.sol

It is unnecessary to initialize variables as they get set to 0 by default in:

	line 244
	line 245
	line 277
	line 278
	
Recommendation:

	uint nextIndex;
        uint index;
        
3.

Contract: BaseV1-core.sol

There are missing spaces in the following lines of code:

	line 225
	line 248
	line 281

Recommendation:

	(uint[] memory _reserves0, uint[] memory _reserves1) = sampleReserves(granularity, 1);
	for(; i < lastIndex; i += window) {
	
All of the above will also bring consistency throughout the code of both contracts.