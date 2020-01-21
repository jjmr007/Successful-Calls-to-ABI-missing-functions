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

*Assembly* instructions are more or less instructing the following:

 - i.- In a *ptr* variable, the data of the function that is to be executed and stored in the memory is stored *from* the 0x40 position, and the amount of data depend on the number that is retrieved from **_calldatasize_**.
 - ii.- In another variable called *result*, the result of calling the implementation contract and executing the orders that are in ptr **is collected**, but, instead of doing it according to the **_call_** command, **delegatecall** executes everything that it has to do as if the contract that does the things is the invoker, and everything that is written and every state that is changed is done on the invoking contract, and not on the invoked one. This is masterfully explained in the note that [CENTER](https://medium.com/centre-blog/designing-an-upgradeable-ethereum-contract-3d850f637794) prepared that essentially for this innovative strategy, and that without a doubt is an advance in the state of the art of these financial technologies.

   - The important thing to rescue here is that when a contract invokes the execution of one function of another contract by means of the **delegatecall** command, all the changes of state, everything that is written, every fund that is received, arrives, is written and updated **_in the invoker contract_**, *not in the invoked one*!

   ![image](delegatecall.PNG)

 - iii.- This means that it is not necessary for the unnamed function to return any arguments, since the *returndatacopy* assembly instruction will store in memory everything that needs to be recovered! (So it is unnecessary for the fallback function to explicitly return any argument, since these are housed in the volatile memory of the ethereum virtual machine and can be rescued through the interface that the user is using).

All this means that if the *implementation* function to be executed is replaced; without anyone even noticing, the logic that will begin to execute the contract will have been updated on, without altering the data or the balance values of any of the users!

No. 7 So in order to properly execute the [True-USD](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#code) contract, all we need is to rescue the ABI from the implementation function, unless we want to execute some native control function of the contract in question, such as modifying the contract of implementation or change the contract owner. The value of the address of the implementation contract in the case of Trust-USD is public and [easily fetchable](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#readContract) as "*implementation*", and its value is `0xcb9a11afdc6bdb92e4a6235959455f28758b34ba`. Therefore, the [ABI](https://etherscan.io/address/0xcb9a11afdc6bdb92e4a6235959455f28758b34ba#code) of the implementation contract is easily salvageable. This time it is much more extensive data:

```cmd
>var abi = [{"constant":true,"inputs":[],"name":"burnMin","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":false,"inputs":[{"name":"_spender","type":"address"},{"name":"_value","type":"uint256"}],"name":"approve","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"owner","type":"address"},{"name":"spender","type":"address"}],"name":"delegateAllowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_minimumGasPriceForFutureRefunds","type":"uint256"}],"name":"setMinimumGasPriceForFutureRefunds","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_from","type":"address"},{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[],"name":"sponsorGas","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateApprove","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_ownable","type":"address"}],"name":"reclaimContract","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"rounding","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":true,"inputs":[],"name":"minimumGasPriceForFutureRefunds","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"mint","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_value","type":"uint256"}],"name":"burn","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"who","type":"address"}],"name":"delegateBalanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"from","type":"address"},{"name":"to","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateTransferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[],"name":"claimOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_min","type":"uint256"},{"name":"_max","type":"uint256"}],"name":"setBurnBounds","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"addedValue","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateIncreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"minimumGasPriceForRefund","outputs":[{"name":"result","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"burnMax","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"paused","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":false,"inputs":[{"name":"_spender","type":"address"},{"name":"_subtractedValue","type":"uint256"}],"name":"decreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_who","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"delegateTotalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"registry","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"remainingGasRefundPool","outputs":[{"name":"length","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"token","type":"address"},{"name":"_to","type":"address"}],"name":"reclaimToken","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"subtractedValue","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateDecreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"symbol","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"}],"name":"reclaimEther","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"to","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateTransfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_registry","type":"address"}],"name":"setRegistry","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_account","type":"address"}],"name":"wipeBlacklistedAccount","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[],"name":"sponsorGas2","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_spender","type":"address"},{"name":"_addedValue","type":"uint256"}],"name":"increaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_who","type":"address"},{"name":"_spender","type":"address"}],"name":"allowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"pendingOwner","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"_index","type":"uint256"}],"name":"gasRefundPool","outputs":[{"name":"gasPrice","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"newOwner","type":"address"}],"name":"transferOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_who","type":"address"},{"name":"_attribute","type":"bytes32"},{"name":"_value","type":"uint256"}],"name":"syncAttributeValue","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"account","type":"address"},{"indexed":false,"name":"balance","type":"uint256"}],"name":"WipeBlacklistedAccount","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"registry","type":"address"}],"name":"SetRegistry","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"name":"newMin","type":"uint256"},{"indexed":false,"name":"newMax","type":"uint256"}],"name":"SetBurnBounds","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"burner","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Burn","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Mint","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"owner","type":"address"},{"indexed":true,"name":"spender","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"from","type":"address"},{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"previousOwner","type":"address"},{"indexed":true,"name":"newOwner","type":"address"}],"name":"OwnershipTransferred","type":"event"}]
undefined 
>_
```
And now by inspecting the ABI we get

```cmd
>abi 
```

![image](abi1.PNG)	![image](abi2.PNG)

Where the most *common* functions of an ERC20 token are observed.

Now, it is indeed possible to create the appropriate instance of the contract:

```cmd
>var interface = '0xcb9a11afdc6bdb92e4a6235959455f28758b34ba'
undefined 
>var token = '0x0000000000085d4780B73119b644AE5ecd22b376'
undefined 
>var contract = new web3.eth.Contract(abi, token)
undefined 
>_
```

And we can get the *executable* methods from the token through the interface:

```cmd
>contract.methods
```

There is another way of doing this by using [MyEtherWallet](https://www.myetherwallet.com/access-my-wallet) where, when we start the Metamask wallet, we go to the contracts section, we place the token addres and in the ABI section, we place the .json data of the implementation contract (taking care of having chosen the correct network , in this case MAIN):

![image](MyEth1.PNG)
![image](MyEth2.PNG)
![image](MyEth3.PNG)
![image](MyEth4.PNG)
![image](MyEth5.PNG)
![image](MyEth7.PNG)

From the console, similar methods can be try:

```cmd
>contract.methods.name().call(function(err, result) { console.log(result) })
Promise { <pending> }
> TrueUSD
```

```cmd
>contract.methods.totalSupply().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 144003798740000000000000000
``` 

The reason for passing the results of *call()* to a function and printing these results on the console is due to the asynchronous nature in the interface between javascript and web3.eth.

```cmd
>contract.methods.decimals().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 18
``` 

```cmd
>contract.methods.symbol().call(function(err, result) { console.log(result) })
Promise { <pending> }
> TUSD
``` 

As an exercise, let us note that if we had used the interface address as the contract address, even though it legitimately has the ABI code that we have executed, we will find that on this contract there is hardly anything written. That is, it has a total Supply of zero, nobody has balances, etc.

```cmd
>var contract = new web3.eth.Contract(abi, interface)
undefined
>contract.methods.totalSupply().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 0
``` 

```cmd
>contract.methods.balanceOf('0x8bb38C74B8aaf929201f013C9ECc42b750E562c6').call(function(err, result) { console.log(result) })
Promise { <pending> }
> 0
``` 

N ° 8 Before closing the interesting case of Trust-Coin, it should be said that these entrepreneurs were pioneers in the strategy of updating the platform. His first plan was primitive, and was to copy the data to a new contract, as the tokens were mobilized. In this way a "[Legacy](https://etherscan.io/address/0x8dd5fbCe2F6a956C3022bA3663759011Dd51e73E#code)" contract remains for posterity, against which even today and indefinitely, token transactions can continue to be carried out completely normally.

If we repeat the previous exercises with legacy we will find that this contract retains its data as a perfect mirror of the official active contract data:

```cmd
>var legacy = '0x8dd5fbCe2F6a956C3022bA3663759011Dd51e73E'
undefined
>_
``` 

Of course, ABI data must be replaced by the corresponding to the original Trust-USD contract:

```cmd
>var abi = [{"constant":true,"inputs":[],"name":"burnMin","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"value","type":"uint256"}],"name":"approve","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"owner","type":"address"},{"name":"spender","type":"address"}],"name":"delegateAllowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"burnFeeFlat","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_canReceiveMintWhiteList","type":"address"},{"name":"_canBurnWhiteList","type":"address"},{"name":"_blackList","type":"address"},{"name":"_noFeesList","type":"address"}],"name":"setLists","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"token","type":"address"}],"name":"reclaimToken","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"newContract","type":"address"}],"name":"delegateToNewContract","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_transferFeeNumerator","type":"uint80"},{"name":"_transferFeeDenominator","type":"uint80"},{"name":"_mintFeeNumerator","type":"uint80"},{"name":"_mintFeeDenominator","type":"uint80"},{"name":"_mintFeeFlat","type":"uint256"},{"name":"_burnFeeNumerator","type":"uint80"},{"name":"_burnFeeDenominator","type":"uint80"},{"name":"_burnFeeFlat","type":"uint256"}],"name":"changeStakingFees","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"canReceiveMintWhiteList","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"from","type":"address"},{"name":"to","type":"address"},{"name":"value","type":"uint256"}],"name":"transferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"delegatedFrom","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateApprove","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"contractAddr","type":"address"}],"name":"reclaimContract","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"allowances","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"unpause","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_amount","type":"uint256"}],"name":"mint","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_value","type":"uint256"}],"name":"burn","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"who","type":"address"}],"name":"delegateBalanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"from","type":"address"},{"name":"to","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateTransferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[],"name":"claimOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"sheet","type":"address"}],"name":"setBalanceSheet","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"addedValue","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateIncreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"burnFeeNumerator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"canBurnWhiteList","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"burnMax","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"paused","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"mintFeeDenominator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"staker","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"addr","type":"address"}],"name":"setDelegatedFrom","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"subtractedValue","type":"uint256"}],"name":"decreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"noFeesList","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"who","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"newMin","type":"uint256"},{"name":"newMax","type":"uint256"}],"name":"changeBurnBounds","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"delegateTotalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"balances","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"pause","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"string"},{"name":"_symbol","type":"string"}],"name":"changeName","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"mintFeeNumerator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"transferFeeNumerator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"subtractedValue","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateDecreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"symbol","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"to","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateTransfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[],"name":"reclaimEther","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"to","type":"address"},{"name":"value","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"newStaker","type":"address"}],"name":"changeStaker","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"account","type":"address"}],"name":"wipeBlacklistedAccount","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"from_","type":"address"},{"name":"value_","type":"uint256"},{"name":"data_","type":"bytes"}],"name":"tokenFallback","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"burnFeeDenominator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"delegate","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"blackList","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"transferFeeDenominator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"mintFeeFlat","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"addedValue","type":"uint256"}],"name":"increaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_owner","type":"address"},{"name":"spender","type":"address"}],"name":"allowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"pendingOwner","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"sheet","type":"address"}],"name":"setAllowanceSheet","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"newOwner","type":"address"}],"name":"transferOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"inputs":[],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"payable":false,"stateMutability":"nonpayable","type":"fallback"},{"anonymous":false,"inputs":[{"indexed":false,"name":"newMin","type":"uint256"},{"indexed":false,"name":"newMax","type":"uint256"}],"name":"ChangeBurnBoundsEvent","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"amount","type":"uint256"}],"name":"Mint","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"account","type":"address"},{"indexed":false,"name":"balance","type":"uint256"}],"name":"WipedAccount","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"newContract","type":"address"}],"name":"DelegatedTo","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"burner","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Burn","type":"event"},{"anonymous":false,"inputs":[],"name":"Pause","type":"event"},{"anonymous":false,"inputs":[],"name":"Unpause","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"previousOwner","type":"address"},{"indexed":true,"name":"newOwner","type":"address"}],"name":"OwnershipTransferred","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"owner","type":"address"},{"indexed":true,"name":"spender","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"from","type":"address"},{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Transfer","type":"event"}]
undefined
>_
``` 

```cmd
>var contract = new web3.eth.Contract(abi, interface)
undefined
>contract.methods.totalSupply().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 144003798740000000000000000
```

