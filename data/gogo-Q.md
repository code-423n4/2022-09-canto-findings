# 2022-09-CANTO
## Low Risk and Non-Critical Issues

### Events not emmited on important state changes
Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

_There are **5** instances of this issue:_

```solidity
File: src/Swap/BaseV1-core.sol

555:  function setAdmin(address admin_) external {

575:  function setPauser(address _pauser) external {

580:  function acceptPauser() external {

585:  function setPause(bool _state) external {
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol

```solidity
File: src/Swap/BaseV1-periphery.sol

89:   function setAdmin(address admin_) external {
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol

### No NatSpec makes it quite difficult to read
Consider adding [natspec](https://docs.soliditylang.org/en/develop/natspec-format.html) comments

_There are **2** instances of this issue:_

```solidity
File: src/Swap/BaseV1-core.sol

      /// @audit
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol

```solidity
File: src/Swap/BaseV1-periphery.sol

      /// @audit
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol

### Event is missing `indexed` fields

_There are **6** instances of this issue:_

```solidity
File: src/Swap/BaseV1-core.sol

event      Mint(address indexed sender, uint amount0, uint amount1)

event      Burn(address indexed sender, uint amount0, uint amount1, address indexed to)

event      Claim(address indexed sender, address indexed recipient, uint amount0, uint amount1)

event      Transfer(address indexed from, address indexed to, uint amount)

event      Approval(address indexed owner, address indexed spender, uint amount)

event      PairCreated(address indexed token0, address indexed token1, bool stable, address pair, uint)
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol

### Lack of input validation in constructor
Check if any address input is accidentally equal to `address(0)` and make sure all of them are deployed contracts using [OpenZeppelin's isContract function](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol#L36-L42).

_There is **1** instance of this issue:_

```solidity
File: src/Swap/BaseV1-periphery.sol

76:   factory = _factory;

78:   wcanto = IWCANTO(_wcanto);

79:   note = note_;

80:   Comptroller = Comptroller_;
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol

### Lack of input validation in `setAdmin` may block admin functionallity
Make sure `admin_` is not equal to `address(0)`. Consider implementing a 2 step ownership transfer by creating a separate function that can be invoked by the new admin in order to accept the ownership.

_There are **2** instances of this issue:_

```solidity
File: src/Swap/BaseV1-core.sol

555:  function setAdmin(address admin_) external {
556:      require(msg.sender == admin);
557:      admin = admin_;
558:  }
```
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L555-L558

```solidity
File: src/Swap/BaseV1-periphery.sol

89:   function setAdmin(address admin_) external {
90:       require(msg.sender == admin);
91:       admin = admin_;
92:   }
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L89-L92

### Not using the `SenderNotAdmin` in `setAdmin`

```solidity
File: src/Swap/BaseV1-core.sol

555:  function setAdmin(address admin_) external {
556:      require(msg.sender == admin);
```
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L555-L556

```solidity
File: src/Swap/BaseV1-periphery.sol

89:   function setAdmin(address admin_) external {
90:       require(msg.sender == admin);
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol#L89-L90

### Do not use `assert` in `receive`
This will result in lossing unnecessary funds (from txn gas limit and gas cost) for the msg.sender

### Not used import

_There are **2** instances of this issue:_

```solidity
File: src/Swap/BaseV1-core.sol

4:    import "./BaseV1-libs.sol";
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol

```solidity
File: src/Swap/BaseV1-periphery.sol

7:    import "./BaseV1-libs.sol";
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol

### Use `immutable` & `constant` for state variables that do not change their value

_There are **3** instances of this issue:_

```solidity
File: src/Swap/BaseV1-core.sol

542:  address internal _temp0;

543:  address internal _temp1;

544:  bool internal _temp;
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-core.sol


### Adding a `return` statement when the function defines a named return variable, is redundant

_There are **1** instances of this issue:_

```solidity
File: src/Swap/BaseV1-periphery.sol

126:  function getAmountOut(uint amountIn, address tokenIn, address tokenOut) external view returns (uint amount, bool stable) {
137:      return amountStable > amountVolatile ? (amountStable, true) : (amountVolatile, false);
```
https://github.com/code-423n4/2022-09-canto/tree/main/src/Swap/BaseV1-periphery.sol