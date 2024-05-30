## Foundry

**Foundry is a blazing fast, portable and modular toolkit for Ethereum application development written in Rust.**

Foundry consists of:

- **Forge**: Ethereum testing framework (like Truffle, Hardhat and DappTools).
- **Cast**: Swiss army knife for interacting with EVM smart contracts, sending transactions and getting chain data.
- **Anvil**: Local Ethereum node, akin to Ganache, Hardhat Network.
- **Chisel**: Fast, utilitarian, and verbose solidity REPL.

## Documentation

https://book.getfoundry.sh/

## Usage

### Build

```shell
$ forge build
```

### Test

```shell
$ forge test
```

### Format

```shell
$ forge fmt
```

### Gas Snapshots

```shell
$ forge snapshot
```

### Anvil

```shell
$ anvil
```

### Deploy

```shell
$ forge script script/Counter.s.sol:CounterScript --rpc-url <your_rpc_url> --private-key <your_private_key>
```

### Cast

```shell
$ cast <subcommand>
```

### Help

```shell
$ forge --help
$ anvil --help
$ cast --help
```

### Writing Tests

```shell
contract FundMeTest is Test {}
```

- deploy contract

```shell
 function setUp() external {
    fundMe = new FundMe();
 }
```

- Test

```shell
function testMinimumDollarIsFive() public {
       assertEq(fundMe.MINIMUM_USD(), 5e18);
   }
```

'assertEq' stands for "assert equal" and is used to check if two values are equal. If they are not, the assertion fails, and the transaction reverts, stopping further execution and returning an error message indicating the failure.

```shell
forge test -vv
```

-vv: This flag increases the verbosity level of the output. In many command-line tools, -v stands for verbose mode, which provides detailed output about the operations being performed. Adding an additional v makes the output even more detailed. In the context of forge test, this means you'll see more information about each test that runs, including potentially more detailed error messages if any tests fail.

- Deploy Script - a seperate deploy script is necessary for security and best practices.

```shell
contract DeployFundMe is Script {
    function run() external returns (FundMe) {
        HelperConfig helperConfig = new HelperConfig();
        address ethUsdPriceFeed = helperConfig.activeNetworkConfig();
        vm.startBroadcast();
        FundMe fundMe = new FundMe(ethUsdPriceFeed);
        vm.stopBroadcast();
        return fundMe;
    }
}
```

- Running a specific Test

```shell
    $ forge test -m testPriceFeedVersionIsAccurate -vvv
```

The -m option specifies a tag or a name of the test(s) to run. In this case, testPriceFeedVersionIsAccurate is the identifier for the specific test you want to execute.
'testPriceFeedVersionIsAccurate' calls a contract that doesn't exist and hence the test fails( when we dont pass an RPC url, foundry spins up an anvil chain and closes it after its action is done). for the test to pass, we need to pass an RPC url

### There are 4 diffrent types of tests

1. Unit
   - testing a specific part of our code
2. Integration
   - Testing how our code works with other parts of our code
3. Forked
   - Testing our code on a simulated real environment
4. Staging
   - Testing our code in a real environment that is not prod

#### Forked Testing

```shell
    $ forge test -m testPriceFeedVersionIsAccurate -vvv --fork-url $SEPOLIA_RPC_URL
```

Anvil will get spun up but it will take a copy of the sepolia_rpc_url, and will simulate all of our transactions as if they are running actually on the sepolia chain. So it will pretend to deploy and read from the sepolia chain as opposed to a completely blank chain.

### Coverage

```shell
$ forge coverage
```

To see how much of our code is actually tested

- We want to make our deployments modular, with different addresses of different chains so w dont have to write seperate code for running in on diffrent chains.

- _mocks_ refer to simplified versions of external contracts that your main contract interacts with during testing. These mock contracts replicate the interface and behavior of the actual external contracts but are used exclusively for testing purposes. The primary goal of using mocks is to isolate the behavior of the contract under test from the complexities and unpredictabilities of interacting with real external contracts or services.

- In helper config :

1.  Deploy mocks when we are on a local anvil chain
2.  Keep track of contract address across different chains

- To run test on a forked chain

```shell
    $ forge test --fork-url $MAINNET_RPC_URL
```
