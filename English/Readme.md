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

