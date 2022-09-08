1. Use scientific notation (e.g. 1e3) rather than exponentiation (e.g. 10**3)

src/Swap/BaseV1-core.sol
30: uint internal constant MINIMUM_LIQUIDITY = 10**3;

2. Constant redefined elsewhere

src/Swap/BaseV1-core.sol
15: uint8 public constant decimals = 18;

3. Variable names that consist of all capital letters should be reserved for constant/immutable variables

25: bytes32 internal DOMAIN_SEPARATOR;

4. Numeric values having to do with time should use time units for readability

45: uint public periodSize = 1800;
