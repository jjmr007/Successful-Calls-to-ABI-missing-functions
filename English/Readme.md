## How to Successfully Execute in a Contract, Functions that SHOULD be there but are not

Since the advent of the "Stable Coins" a whole silent revolution has been unleashed in the world of new financial technologies.

For strong security and resilience reasons in the logistics of operation, any contract that manages a stable coin must include a strategy of "upgradeability".

In the case at hand, we will devote our attention to three emerging relevance currencies: [True-USD](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#code), [USD-Coin](https://etherscan.io/address/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48#code) and [EURO-Stasis](https://etherscan.io/address/0xdb25f211ab05b1c97d595516f45794528a807ad8#code) and we will unravel their magical performance.

When comparing the lines of code of these contracts with the [standard](https://en.bitcoinwiki.org/wiki/ERC20) that an ERC20 token must meet, we may be unpleasantly surprised that they have little or nothing to do with the expected code.

For example, the "totalSupply" function, which is a mandatory method in any ERC20 token, is absent in every line of code in both the [True-USD](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#code) contract and the [USD-Coin](https://etherscan.io/address/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48#code) code too.

Other essential functions such as the "transfer" one or the balance inquiry of each user are absent as well.

How can, therefore these currencies operate and why traditional wallets, such as Metamask, have no problem executing transactions with them?

Next we will inspect these contracts through the [NODEjs](https://nodejs.org/es/) environment using the [web3.js](https://github.com/ethereum/web3.js/#installation) package.

It is convenient in principle, using our preferred command console ([GitBash](https://gitforwindows.org/) works good for me), to check if these tools are installed:

```cmd
>npm -v
```
	
```cmd
>node -v
```

```cmd
> npm view web3 version
```

If any of these queries fail, it is most likely that the installation is required.

Then we can proceed as follows:

N ° 1 We will enter the node console, as our execution environment for our web3 package:

```cmd
C:\Users\MiUsuario\CarpetaLocal\>node
>_
```

N ° 2 We will now request the 'web3' library by storing it in a slightly different variable: Web3 (with a capital 'W').

```cmd
>var Web3 = require('web3')
undefined 
>_
```

N ° 3 The following is to find the URL of some service provider of remote connection to the ethereum network.

One way to do this is through the services of the [INFURA](https://infura.io/) company.

For low traffic cases and test environments, a completely free link can be obtained for both the main network and most test networks.

You have to make a simple subscription and register a project. In return, the platform gives us a URL that will give us indirect access to the ethereum network. This address will have the form of:

```url
mainnet.infura.io/v3/PROJECT_KEY_NUMBER
```

N ° 4 Now we are able to obtain an instance of web3:

```cmd
>var url = 'https://mainnet.infura.io/v3/PROJECT_KEY_NUMBER'
undefined 
>var web3 = new Web3(url)
undefined 
>_
```

No. 5 We now create an instance of the contract that we want to inspect. But there is a small problem: to rebuild this instance we need the address where said contract resides in the blocchain and also, the ABI.json file of the contract.

But if we extract the ABI.json asset from the contract directly deployed from, say, [True-USD](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#code), we will not be able to execute any function worthy of an ERC20 token. How we do?

In the normal case we would do the following: we use some appropriate browser such as [Etherscan](https://etherscan.io/) and look for the contract of our interest. If this seems the same than looking for a needle in a haystack, it is possible to do so by choosing the "Tokens" page, looking the most famous ones. We can also locate the explorer page of a particular token, by finding it on the [CoinMArketCap](https://coinmarketcap.com/) page.

The remarkable thing about all this information is that it is public. So the "ABI contract" can be copied directly from the page where the contract is exposed in etherscan (as long as the owner of the code has [verified](https://github.com/jjmr007/Deploying-and-Verifying-a-Contract-using-Remix-and-Etherscan) the contract).

Once the ABI data is copied, we go to the console and declare it:

```cmd
>var abi = [{"constant":true,"inputs":[],"name":"proxyOwner","outputs":[{"name":"owner","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"pendingProxyOwner","outputs":[{"name":"pendingOwner","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"implementation","type":"address"}],"name":"upgradeTo","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"implementation","outputs":[{"name":"impl","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"claimProxyOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"newOwner","type":"address"}],"name":"transferProxyOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"inputs":[],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"payable":true,"stateMutability":"payable","type":"fallback"},{"anonymous":false,"inputs":[{"indexed":true,"name":"previousOwner","type":"address"},{"indexed":true,"name":"newOwner","type":"address"}],"name":"ProxyOwnershipTransferred","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"name":"currentOwner","type":"address"},{"indexed":false,"name":"pendingOwner","type":"address"}],"name":"NewPendingOwner","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"implementation","type":"address"}],"name":"Upgraded","type":"event"}]
undefined 
>_
```

In this case, the ABI is the one of the official version of the True-USD contract. We can inspect the content of this element when executing:

```cmd
>abi 
```

Then all the content is displayed in an orderly manner and it will be more or less accessible to the naked eye. It will be quite evident that no function of an ERC20 token exists.

![image](abi.PNG)

As expected, nothing of "transfer" or "balanceOf". So, what can we do now?

N ° 6 The first thing that catches our attention is that it is a contract that has already undergone an update. His address number strangely and inexplicably has a sequence of 11 zeros! How did they achieve such eccentricity?

It was used a special application to calculate "[vanity addresses](https://github.com/MyEtherWallet/VanityEth)" that what they do is to generate as in a mining software, multiple private keys for externally controlled address (EOA) in order to deploy a contract several times, until the contract address deployed had the amount of zeros that the algorithm can afford or the user is willing to wait for them to be generated. This was carried out by the [Trust-Token](https://www.trusttoken.com/) company in order to grant an extra security feature to its contract.

On the other hand, the contract does not have the name "True-USD", that was in the past! Now it is called: "OwnedUpgradeabilityProxy" and is the sequence of several code blocks in the form of abstract contracts, inherited one after another until the compilable contract  has the presence of a function without any name, known as [FALLBACK-FUNCTION](https://solidity.readthedocs.io/en/v0.4.23/contracts.html#fallback-function): a function that must be of external access and with no arguments!

According to the solidity documentation:

>"Every contract can have exactly ONE single function without a name. It cannot have arguments and cannot return any value. This function is executed when the contract that contains it is called and none of the functions it has in its ABI *MATCHES* with the function that is being invoked against said contract, or if no data on any function is provided. "

The repository continues:

>"Additionally, this function is executed when a contract receives money (in ethers, and without any other data associated with the transaction) but only when it has been labeled as **_payable_**. If such conditions are not met, the contract rejects any sending of funds in the form of ethereum."

But the curious thing about this function are the instructions that it orders within its internal description:

```js
function() external payable {
        address _impl = implementation();
        require(_impl != address(0), "implementation contract not set");
        
        assembly {
            let ptr := mload(0x40)
            calldatacopy(ptr, 0, calldatasize)
            let result := delegatecall(gas, _impl, ptr, calldatasize, 0, 0)
            let size := returndatasize
            returndatacopy(ptr, 0, size)

            switch result
            case 0 { revert(ptr, size) }
            default { return(ptr, size) }
        }
    }
```

There is an address type variable called "*implementaton*" (_impl) that is only taken into account if it is non-zero (that is, if it has been defined) and **is assumed to be another contract** and that executes a series of instructions at "**_assembly_**" level, and what basically does is to take the data that is being instructed to be executed and is executed by means of the **_DELEGATECALL_** [operational command](https://ethervm.io/).
