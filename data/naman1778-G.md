## [G-01] Use *assembly* to write address storage values

When writing value for variables whose type is address, make use of assembly code instead of solidity code.

There are 6 instances of this issue in 6 files: 

    File: GovernorCharlie.sol	
    
    686: whitelistGuardian = _account;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

    File: USDA.sol	

    64: pauser = _pauser;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol

    File: UFragments.sol	

    112: monetaryPolicy = _monetaryPolicy;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol

    File: WUSDA.sol	

    49: USDA = _usdaToken;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/WUSDA.sol

    File: CurveMaster.sol	

    33: vaultControllerAddress = _vaultMasterAddress;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/CurveMaster.sol

    File: OracleRelay.sol	

    20: underlying = _underlying;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/OracleRelay.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;

        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }

        function testGas() public {
            c0.setOwnerAssembly(0xFD2dabe9DFcc4d88a12A9D0D40D834E81217Cccf);
            c1.setOwner(0xFD2dabe9DFcc4d88a12A9D0D40D834E81217Cccf);
        }
    }

    contract Contract0 {

        address owner;
        function setOwnerAssembly(address _owner) public {
            assembly{
                sstore(owner.slot,_owner)
            }
        }

    }

    contract Contract1 {
        address owner;
        function setOwner(address _owner) public {
            owner = _owner;
        }

    }

#### Gas Report

|  Contract0 contract                       |                 |       |        |       |         |
|-------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                           | Deployment Size |       |        |       |         |
| 35287                                     | 207             |       |        |       |         |
| Function Name                             | min             | avg   | median | max   | # calls |
| setOwnerAssembly                          | 22324           | 22324 | 22324  | 22324 | 1       |

|  Contract1 contract                       |                 |       |        |       |         |
|-------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                           | Deployment Size |       |        |       |         |
| 48499                                     | 273             |       |        |       |         |
| Function Name                             | min             | avg   | median | max   | # calls |
| setOwner                                  | 22363           | 22363 | 22363  | 22363 | 1       |

## [G-02] Use nested if and, avoid multiple check combinations

Using nesting is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

There are 7 instances of this issue in 5 files:

    File: VaultController.sol	

    1018: if (_increase && (_collateral.totalDeposited + _amount) > _collateral.cap) revert VaultController_CapReached();

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol

    File: GovernorCharlie.sol	
    
    170: if (amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender)) {

    208: if (_emergency && !isWhitelisted(msg.sender)) {

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

    File: Vault.sol	

    158: if (_poolId != 0 && balances[_token] != 0 && !isTokenStaked[_token]) _canStake = true;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol

    File: AMPHClaimer.sol	

    88: if (_crvAmountToSend != 0 && _claimedAmph != 0) distributedAmph += (_claimedAmph / 1e12); // scale back to 1e6

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol

    File: ThreeLines0_100.sol	

    34: if (!((0 < _r2) && (_r2 < _r1) && (_r1 < _r0))) revert ThreeLines0_100_InvalidCurve();

    35: if (!((0 < _s1) && (_s1 < _s2) && (_s2 < 1e18))) revert ThreeLines0_100_InvalidBreakpointValues();

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/ThreeLines0_100.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;

        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }

        function testGas() public {
            c0.checkAge(19);
            c1.checkAgeOptimized(19);
        }
    }

    contract Contract0 {

        function checkAge(uint8 _age) public returns(string memory){
            if(_age>18 && _age<22){
                return "Eligible";
            }
        }

    }

    contract Contract1 {

        function checkAgeOptimized(uint8 _age) public returns(string memory){
            if(_age>18){
                if(_age<22){
                    return "Eligible";
                }
            }
        }
    }

#### Gas Report

| Contract0 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 76923                                     | 416             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| checkAge                                  | 651             | 651 | 651    | 651 | 1       |
 
| Contract1 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 76323                                     | 413             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| checkAgeOptimized                         | 645             | 645 | 645    | 645 | 1       |

## [G-03] Using XOR (*^*) and OR (*|*) bitwise equivalents

On Remix, given only *uint256* types, the following are logical equivalents, but don’t cost the same amount of gas:

- *(a != b || c != d || e != f)* costs 571
- *((a ^ b) | (c ^ d) | (e ^ f))* != 0 costs 498 (saving 73 gas)

#### Logic POC
Given 4 variables *a*, *b*, *c* and *d* represented as such:

    0 0 0 0 0 1 1 0 <- a
    0 1 1 0 0 1 1 0 <- b
    0 0 0 0 0 0 0 0 <- c
    1 1 1 1 1 1 1 1 <- d

To have *a == b* means that every *0* and *1* match on both variables. Meaning that a XOR (operator *^*) would evaluate to 0 (*(a ^ b) == 0*), as it excludes by definition any equalities.
Now, if *a != b*, this means that there’s at least somewhere a *1* and a *0* not matching between *a* and *b*, making *(a ^ b) != 0*.

Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas:

    function xOrEquivalence(uint a, uint b) external returns (bool) {
      //return a != b; //370
      //return a ^ b != 0; //370
    }

However, it is much cheaper to use the bitwise OR operator (*|*) than comparing the truthy or falsy values:

    function xOrOrEquivalence(uint a, uint b, uint c, uint d) external returns (bool) {
        //return (a != b || c != d); // 495
        //return (a ^ b | c ^ d) != 0; // 442
    }

These are logically equivalent too, as the OR bitwise operator (*|*) would result in a *1* somewhere if any value is not 0 between the XOR (*^*) statements, meaning if any XOR (*^*) statement verifies that its arguments are different.

There are 3 instances of this issue in 3 files:

    File: GovernorCharlie.sol	
    
    174: _targets.length != _values.length || _targets.length != _signatures.length || _targets.length != _calldatas.length

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

    File: UFragments.sol	

    57: if (_to == address(0) || _to == address(this)) revert UFragments_InvalidRecipient();

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol

    File: AMPHClaimer.sol	

    169: if (_crvTotalRewards == 0 || _amphBalance == 0) return (0, 0, 0);

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/AMPHClaimer.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized(5,10,16);
            c1.optimized(5,10,16);
        }
    }
    contract Contract0 {
        address a;
        function not_optimized(uint8 x,uint8 y, uint8 z) public returns(bool){
            if(x!=5 || y!=10 || z!=15){
                return true;
            }
        }
    }
    contract Contract1 {
        address a;
        function optimized(uint8 x,uint8 y, uint8 z) public returns(bool){
            if(((x^5) | (y^10) | (z^15)) != 0){
                return true;
            }
        }
    }
    

#### Gas Report

| Contract0 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 53705                                     | 300             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| not_optimized                             | 622             | 622 | 622    | 622 | 1       |
    
| Contract1 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 49899                                     | 280             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| optimized                                 | 569             | 569 | 569    | 569 | 1       |

## [G-04] Stack variable used as a cheaper cache for a state variable is only used once

If the variable is only accessed once, it’s cheaper to use the state variable directly that one time, and save the 3 gas the extra stack assignment would spend.

There are 8 instances of this issue in 3 files:

    File: VaultController.sol	

    512: uint256 _liquidationIncentive = tokenAddressCollateralInfo[_assetAddress].liquidationIncentive;

    676: CollateralInfo memory _assetInfo = tokenAddressCollateralInfo[_assetAddress];

    786: uint256 _denominator = _badFillPrice - _ltvDiscount;

    874: address _tokenAddress = enabledTokens[_i];

    902: address _tokenAddress = enabledTokens[_i];

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol

    File: AnchoredViewRelay.sol	

    91: uint256 _upperBounds = _anchorPrice + _buffer;

    92: uint256 _lowerBounds = _anchorPrice - _buffer;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol

    File: CbEthEthOracle.sol	

    55: uint256 _vp = virtualPrice;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/CbEthEthOracle.sol

#### Test Code 

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized();
            c1.optimized();
        }
    }
    
    contract Contract0 {
        uint256 num = 5;
        uint256 sum;
        function not_optimized() public returns(bool){
            uint256 num1 = num;
            sum = num1 + 2;
        }
    }
    
    contract Contract1 {
        uint256 num = 5;
        uint256 sum;
        function optimized() public returns(bool){
            sum = num + 2;
        }
    }

#### Gas Report

| Contract0 contract                        |                 |       |        |       |         |
|-------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                           | Deployment Size |       |        |       |         |
| 63799                                     | 244             |       |        |       |         |
| Function Name                             | min             | avg   | median | max   | # calls |
| not_optimized                             | 24462           | 24462 | 24462  | 24462 | 1       |

| Contract1 contract                        |                 |       |        |       |         |
|-------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                           | Deployment Size |       |        |       |         |
| 63599                                     | 243             |       |        |       |         |
| Function Name                             | min             | avg   | median | max   | # calls |
| optimized                                 | 24460           | 24460 | 24460  | 24460 | 1       |

## [G-05] Instead of counting down in *for* statements, count up

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable.

There are 13 instances of this issue in 5 files:

    File: VaultController.sol	

    255: for (uint256 _i; _i < _tokenAddresses.length;) {
    268:     ++_i;

    868: for (uint192 _i; _i < enabledTokens.length; ++_i) {

    896: for (uint192 _i; _i < enabledTokens.length; ++_i) {

    998: for (uint256 _j; _j < enabledTokens.length;) {
    1002:     ++_j;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/VaultController.sol

    File: GovernorCharlie.sol	
    
    259: for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

    313: for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

    382: for (uint256 _i = 0; _i < _proposal.targets.length; _i++) {

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

    File: Vault.sol	

    169: for (uint256 _i; _i < _tokenAddresses.length;) {
    202:     ++_i;

    188: for (uint256 _j; _j < _extraRewards;) {
    198:     ++_j;

    254: for (_i; _i < _rewardsAmount;) {
    261:     ++_i;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/Vault.sol

    File: USDA.sol	

    48: for (uint256 _i; _i < _vaultControllers.length();) {
    51:     _i++;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/core/USDA.sol

    File: StableCurveLpOracle.sol	

    22: for (uint256 _i; _i < _anchoredUnderlyingTokens.length;) {
    26:     ++_i;

    44: for (uint256 _i = 1; _i < anchoredUnderlyingTokens.length;) {
    47:     ++_i;

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/StableCurveLpOracle.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.AddNum();
            c1.AddNum();
        }
    }


    contract Contract0 {
        uint256 num = 3;
        function AddNum() public {
            uint256 _num = num;
            for(uint i=0;i<=9;i++){
                _num = _num +1;
            }
            num = _num;
        }
    }


    contract Contract1 {
        uint256 num = 3;
        function AddNum() public {
            uint256 _num = num;
            for(uint i=9;i>=0;i--){
                _num = _num +1;
            }
            num = _num;
        }
    }

#### Gas Report

| Contract0 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 77011                                     | 311             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| AddNum                                    | 7040            | 7040 | 7040   | 7040 | 1       |

| Contract1 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 73811                                     | 295             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| AddNum                                    | 3819            | 3819 | 3819   | 3819 | 1       |


## [G-06] *> 0* is less efficient than *!= 0* for unsigned integers

*!= 0* costs less gas compared to *> 0* for unsigned integers in *require* statements with the optimizer enabled (6 gas)

Proof: While it may seem that *> 0* is cheaper than *!=0*, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a *require* statement, this will save gas. You can see this tweet for more proofs:

https://twitter.com/gzeon/status/1485428085885640706

I suggest changing *> 0* with *!= 0* here:

There are 2 instances of this issue in 1 file:

    File: AnchoredViewRelay.sol	

    77: require(_mainValue > 0, 'invalid oracle value');

    80: require(_anchorPrice > 0, 'invalid anchor value');

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/AnchoredViewRelay.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.func1();
            c1.func1();
        }
    }
    
    contract Contract0 {
        uint256 num=2;
    
        function func1() public {
             require(num > 0,"Should be greater than zero");
        }
    }
    
    contract Contract1 {
        uint256 num=2;
    
        function func1() public {
             require(num != 0,"Should be greater than zero");
        }
    }

#### Gas Report

| Contract0 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 68005                                     | 265             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| func1                                     | 2245            | 2245 | 2245   | 2245 | 1       |

| Contract1 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 67605                                     | 263             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| func1                                     | 2239            | 2239 | 2239   | 2239 | 1       |

## [G-07] IF’s/require() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case.

There are 2 instances of this issue in 2 files:

    File: GovernorCharlie.sol	
    
    531: function _castVoteInternal(
    532:   address _voter,
    533:   uint256 _proposalId,
    534:   uint8 _support
    535: ) internal returns (uint96 _numberOfVotes) {
    536:   if (state(_proposalId) != ProposalState.Active) revert GovernorCharlie_VotingClosed();
    537:   if (_support > 2) revert GovernorCharlie_InvalidVoteType();
    - 538:   Proposal storage _proposal = proposals[_proposalId];
    539:   Receipt storage _receipt = proposalReceipts[_proposalId][_voter];
    540:   if (_receipt.hasVoted) revert GovernorCharlie_AlreadyVoted();
    +       Proposal storage _proposal = proposals[_proposalId];
    541:   uint96 _votes = amph.getPriorVotes(_voter, _proposal.startBlock);

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/governance/GovernorCharlie.sol

    File: UFragments.sol	

    315:  function permit(
    316:    address _owner,
    317:    address _spender,
    318:    uint256 _value,
    319:    uint256 _deadline,
    320:    uint8 _v,
    321:    bytes32 _r,
    322:    bytes32 _s
    323:  ) public {
    324:    require(block.timestamp <= _deadline);
    325:  
    - 326:    uint256 _ownerNonce = _nonces[_owner];
    - 327:    bytes32 _permitDataDigest = keccak256(abi.encode(PERMIT_TYPEHASH, _owner, _spender, _value, _ownerNonce, _deadline));
    - 328:    bytes32 _digest = keccak256(abi.encodePacked('\x19\x01', DOMAIN_SEPARATOR(), _permitDataDigest));
    329:  
    330:    if (uint256(_s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) {
    331:      revert UFragments_InvalidSignature();
    332:    }
    +        uint256 _ownerNonce = _nonces[_owner];
    +        bytes32 _permitDataDigest = keccak256(abi.encode(PERMIT_TYPEHASH, _owner, _spender, _value, _ownerNonce, _deadline));
    +        bytes32 _digest = keccak256(abi.encodePacked('\x19\x01', DOMAIN_SEPARATOR(), _permitDataDigest));

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/utils/UFragments.sol

## [G-08] *internal* functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the *override* keyword.

There is 1 instance of this issue 1 file:

    File: EthSafeStableCurveOracle.sol	

    48: function _getVirtualPrice() internal view override returns (uint256 _value) {

https://github.com/code-423n4/2023-07-amphora/blob/main/core/solidity/contracts/periphery/oracles/EthSafeStableCurveOracle.sol