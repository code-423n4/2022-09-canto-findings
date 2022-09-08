### [L-01] ```require()``` should be used instead of ```assert()```


#### Impact
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as ```require()```/```revert()``` do. ```assert()``` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.


#### Findings:
```
./BaseV1-periphery.sol:L85        assert(msg.sender == address(wcanto)); // only accept ETH via fallback from the WETH contract

./BaseV1-periphery.sol:L236                assert(amountAOptimal <= amountADesired);

./BaseV1-periphery.sol:L282        assert(wcanto.transfer(pair, amountCANTO));

./BaseV1-periphery.sol:L428        assert(wcanto.transfer(pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]));

```

### [L-02] ```decimals()``` not part of ERC20 standard.


#### Impact
```decimals()``` is not part of the official ERC20 standard and might fall for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.


#### Findings:
```
./BaseV1-core.sol:L93        decimals0 = 10**erc20(_token0).decimals();

./BaseV1-core.sol:L94        decimals1 = 10**erc20(_token1).decimals();

./BaseV1-periphery.sol:L502            uint decimals = erc20(underlying).decimals();

./BaseV1-periphery.sol:L506            uint decimals = erc20(underlying).decimals();

./BaseV1-periphery.sol:L531        uint decimals = 10 ** token.decimals(); // get decimals of token

./BaseV1-periphery.sol:L543        uint decimals = 10 ** token.decimals();

./BaseV1-periphery.sol:L560                decimals = 10 ** (erc20(token1).decimals()); // we must normalize the price of token1 to 18 decimals

./BaseV1-periphery.sol:L564                decimals = 10 ** (erc20(token0).decimals());

./BaseV1-periphery.sol:L570                decimals = 10 ** (erc20(token1).decimals());

./BaseV1-periphery.sol:L574                decimals = 10 ** (erc20(token0)).decimals();

```

### [L-03] Use of ```ecrecover``` is susceptible to signature malleability


#### Impact
The ```ecrecover``` function is used to recover the address from the signature. The built-in EVM precompile ecrecover is susceptible to signature malleability which could lead to replay attacks (references: https://swcregistry.io/docs/SWC-117, https://swcregistry.io/docs/SWC-121 and https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57).

Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.


#### Findings:
```
./BaseV1-core.sol:L488        address recoveredAddress = ecrecover(digest, v, r, s);
```

### [L-04] ```require()```/```revert()``` statements should have descriptive strings.


#### Impact
Consider adding descriptive strings in ```require()```/```revert()```.


#### Findings:
```
./BaseV1-core.sol:L100        require(msg.sender == factory);

./BaseV1-core.sol:L108        require(_unlocked == 1);

./BaseV1-core.sol:L342        require(!BaseV1Factory(factory).isPaused());

./BaseV1-core.sol:L523        require(token.code.length > 0);

./BaseV1-core.sol:L526        require(success && (data.length == 0 || abi.decode(data, (bool))));

./BaseV1-core.sol:L556        require(msg.sender == admin);

./BaseV1-core.sol:L561        require(msg.sender == admin);

./BaseV1-core.sol:L562        require(newPeriod <= MaxPeriod);

./BaseV1-core.sol:L576        require(msg.sender == pauser);

./BaseV1-core.sol:L581        require(msg.sender == pendingPauser);

./BaseV1-core.sol:L586        require(msg.sender == pauser);

./BaseV1-periphery.sol:L90        require(msg.sender == admin);

./BaseV1-periphery.sol:L219        require(amountADesired >= amountAMin);

./BaseV1-periphery.sol:L220        require(amountBDesired >= amountBMin);

./BaseV1-periphery.sol:L300        require(IBaseV1Pair(pair).transferFrom(msg.sender, pair, liquidity)); // send liquidity to pair

./BaseV1-periphery.sol:L465        require(token.code.length > 0);

./BaseV1-periphery.sol:L468        require(success && (data.length == 0 || abi.decode(data, (bool))));

```
### [L-05] ```_safemint()``` should be used rather than ```_mint()``` wherever possible


#### Impact
```_mint()``` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of ```_safeMint()``` which ensures that the recipient is either an EOA or implements ```IERC721Receiver```. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function


#### Findings:
```
./BaseV1-core.sol:L305            _mint(address(0), MINIMUM_LIQUIDITY); // permanently lock the first MINIMUM_LIQUIDITY tokens

./BaseV1-core.sol:L311        _mint(to, liquidity);

./BaseV1-core.sol:L451    function _mint(address dst, uint amount) internal {
```
### [L-06] Events not emitted for important state changes.


#### Impact
When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.


#### Findings:
```
./BaseV1-core.sol:L99             function setPeriodSize(uint periodSize_) external {

./BaseV1-core.sol:L555             function setAdmin(address admin_) external {

./BaseV1-core.sol:L560             function setPeriodSize(uint newPeriod) external {

./BaseV1-core.sol:L575             function setPauser(address _pauser) external {

./BaseV1-core.sol:L580             function acceptPauser() external {

./BaseV1-core.sol:L585             function setPause(bool _state) external {

./BaseV1-core.sol:L610                 emit PairCreated(token0, token1, stable, pair, allPairs.length);

./BaseV1-periphery.sol:L89             function setAdmin(address admin_) external {

./BaseV1-periphery.sol:L478             function setStable(address underlying) external returns (uint) {

./test/Token.sol:L55                 emit Approval(msg.sender, _spender, _value);

./test/Token.sol:L72                 emit Approval(owner, spender, value);

./test/Token.sol:L90                 emit Transfer(address(0x0), _to, _amount);

./test/Token.sol:L101                 emit Transfer(_from, _to, _value);

./test/Token.sol:L139                 emit Transfer(account, address(0), amount);

./test/Token.sol:L148                 emit LogChangeVault(anyswapRouter, _pendingRouter, pendingRouterDelay);

```
### [L-07] Unsafe ERC20 Operation(s)


#### Impact
ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.


#### Findings:
```
./BaseV1-periphery.sol:L282        assert(wcanto.transfer(pair, amountCANTO));

./BaseV1-periphery.sol:L300        require(IBaseV1Pair(pair).transferFrom(msg.sender, pair, liquidity)); // send liquidity to pair

./BaseV1-periphery.sol:L428        assert(wcanto.transfer(pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]));

./BaseV1-periphery.sol:L475        tokenCon.transferFrom(from, to, value);

```
