Hello, team. 

Found strange math function here:
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-libs.sol#L18 

It handles all y cases except 0. If y = 0, function gonna crash, because this value is not > 3 and is not != 0. 
So, this case is unhandled. 
I think for y = 0 function should straightly return z = 0.