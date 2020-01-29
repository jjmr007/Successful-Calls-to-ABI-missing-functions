## Como Ejecutar Exitosamente en un Contrato, Funciones que DEBERÍAN estar allí pero no están

Desde el advenimiento de las "Stable Coins" toda una revolución silenciosa se ha venido desatando en el mundo de las nuevas tecnologías financieras. 

Por fuertes razones de seguridad y resiliencia en la logística de operación, todo contrato que administra una stable coin, debe  incluir una estrategia de "actualizabilidad".

En el caso que nos ocupa, dedicaremos nuestra atención a tres monedas de relevancia emergente: [True-USD](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#code), [USD-Coin](https://etherscan.io/address/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48#code) y [EURO-Stasis](https://etherscan.io/address/0xdb25f211ab05b1c97d595516f45794528a807ad8#code) y vamos a desentrañar su mágico funcionamiento.

Al comparar las líneas de código de estos contratos con el [estándar](https://en.bitcoinwiki.org/wiki/ERC20) que se espera debe cumplir un token ERC20, nos damos con la sorpresa quizá desagradable de que poco o nada tiene que ver su código con lo esperado.

Por ejemplo, la función "totalSupply" que es un método obligatorio en un ERC20, no está por ninguna línea de código tanto en el contrato [True-USD](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#code), como en el código de [USD-Coin](https://etherscan.io/address/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48#code). Tampoco están otras funciones esenciales como la de "transferencia" o la de consulta de saldo de cada usuario. ¿Como pueden operar entonces estas monedas y porque las carteras tradicionales, como Metamask, no tienen ningún problema en ejecutar transacciones con ellas?

A continuación vamos a inspeccionar estos contratos a través del entorno [NODEjs](https://nodejs.org/es/) utilizando el paquete [web3.js](https://github.com/ethereum/web3.js/#installation). Es conveniente en principio, utilizando nuestra consola de comandos de preferencia (A mí me funciona mejor [GitBash](https://gitforwindows.org/) ), verificar si estas herramientas están instaladas:

```cmd
>npm -v
```
	
```cmd
>node -v
```

```cmd
> npm view web3 version
```

Si alguna de estas consultas falla, lo más probable es que la instalación es requerida.

### I.- El Caso de [True-USD](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#code)

A continuación podemos proceder del siguiente modo:

**N° 1** Ingresaremos a la consola de node, como nuestro ambiente de ejecución para nuestro paquete web3:

```cmd
C:\Users\MiUsuario\CarpetaLocal\>node
>_

```

**N° 2** Ahora solicitaremos la biblioteca 'web3' almacenándola en una variable ligeramente diferente: Web3 (con 'W' mayúscula).

```cmd
>var Web3 = require('web3')
undefined 
>_
```

**N° 3** Lo siguiente es encontrar la URL de algún servicio proveedor de conexión remota a la red de ethereum. Una forma de hacerlo es mediante los servicios de la empresa [INFURA](https://infura.io/). Para un trafico bajo y entornos de prueba, se puede obtener un enlace totalmente gratuito tanto para la red principal como para la mayoría de las redes de prueba.

Hay que realizar una sencilla suscripción y registrar un proyecto. A cambio la plataforma nos da una dirección URL que nos dará acceso indirecto a la red ethereum. Esta dirección tendrá la forma de:

```js
mainnet.infura.io/v3/PROJECT_KEY_NUMBER
```

**N° 4** Ahora estamos en capacidad de obtener una instancia de web3:

```cmd
>var url = 'https://mainnet.infura.io/v3/PROJECT_KEY_NUMBER'
undefined 
>var web3 = new Web3(url)
undefined 
>_
```

**N° 5** Creamos ahora una instancia del contrato que se desea inspeccionar. Pero hay un pequeño problema: para reconstruir dicha instancia necesitamos la dirección donde dicho contrato reside en la blockchain y además, el archivo ABI.json del contrato.

Pero si extraemos el archivo ABI.json del contrato directamente desplegado de, digamos, [True-USD](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#code), no vamos a ser capaces de ejecutar ninguna función digna de un token ERC20. ¿Como hacemos?

En el caso normal haríamos lo siguiente: usamos algún explorador apropiado como por ejemplo [Etherscan](https://etherscan.io/) y buscamos el contrato de nuestro interés. Si esto pareciera buscar una aguja en un pajar, es posible hacerlo mediante la opción de la página de los "Tokens", los más famosos. También podemos ubicar la página exploradora de un token en particular, al encontrarlo en la página [CoinMArketCap](https://coinmarketcap.com/).

Lo notable de toda esta información es que es pública. De modo que el "contract ABI" podremos copiarlo directamente de la página donde el contrato esta expuesto en etherscan (siempre y cuando el dueño del código haya [verificado](https://github.com/jjmr007/Deploying-and-Verifying-a-Contract-using-Remix-and-Etherscan) el contrato).

Una vez copiada la data del ABI, vamos a la consola y la declaramos:

```cmd
>var abi = [{"constant":true,"inputs":[],"name":"proxyOwner","outputs":[{"name":"owner","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"pendingProxyOwner","outputs":[{"name":"pendingOwner","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"implementation","type":"address"}],"name":"upgradeTo","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"implementation","outputs":[{"name":"impl","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"claimProxyOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"newOwner","type":"address"}],"name":"transferProxyOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"inputs":[],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"payable":true,"stateMutability":"payable","type":"fallback"},{"anonymous":false,"inputs":[{"indexed":true,"name":"previousOwner","type":"address"},{"indexed":true,"name":"newOwner","type":"address"}],"name":"ProxyOwnershipTransferred","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"name":"currentOwner","type":"address"},{"indexed":false,"name":"pendingOwner","type":"address"}],"name":"NewPendingOwner","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"implementation","type":"address"}],"name":"Upgraded","type":"event"}]
undefined 
>_
```

En este caso el ABI es el de la versión oficial del contrato [True-USD](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#code). Podemos inspeccionar el contenido de este elemento al ejecutar:

```cmd
>abi 
```

A continuación se despliega de manera ordenada todo el contenido y será más o menos accesible a simple vista. Será bastante evidente que ninguna función de un token ERC20 existe.

![image](abi.PNG)

Como era de esperar, nada de "transfer" o de "balanceOf". Y ahora ¿Que hacemos?

**N° 6** Lo primero que nos llama la atención es que se trata de un contrato que ya sufrió una actualización. Su número de address extraña e inexplicablemente posee una secuencia de 11 ceros! ¿cómo lograron semejante excentricidad?

Utilizando una aplicación especial para calcular "[direcciones vanidosas](https://github.com/MyEtherWallet/VanityEth)" que lo que hacen es generar del mismo modo que un software de minería, múltiples llaves privadas para la dirección externamente controlada (EOA) que ha de ordenar el despliegue de un contrato, hasta que el contrato desplegado posea la cantidad de ceros que el algoritmo pueda costear o el usuario este dispuesto a esperar que sean generados. Esto lo llevó a cabo la empresa [Trust-Token](https://www.trusttoken.com/) con el fin de otorgar una cualidad de seguridad extra a su contrato.

Por otro lado, el contrato no lleva como nombre "True-USD", eso quedó en el pasado!. Ahora se llama: "OwnedUpgradeabilityProxy" y es la secuencia de varios bloques de códigos en forma de contratos abstractos, heredados uno tras otro hasta el contrato efectivamente compilable y cuya característica principal es la presencia de una función sin nombre alguno, conocida como [FALLBACK-FUNCTION](https://solidity.readthedocs.io/en/v0.4.23/contracts.html#fallback-function): una función obligatoriamente de acceso externo y que no posee argumentos!.

Según la documentación de solidity: 

>"todo contrato puede tener exactamente UNA sola función sin nombre. No puede tener argumentos y no puede retornar ningún valor. Esta función se ejecuta cuando el contrato que la contiene es llamado y ninguna de las funciones que posee en su ABI *COINCIDE* con la función que esta siendo invocada contra dicho contrato, o si ninguna data sobre función alguna es suministrada".

Continúa el repositorio:

>"Adicionalmente esta función se ejecuta cuando un contrato recibe dinero (en ethers, y sin ninguna otra data asociada a la transacción) pero solamente cuando ha sido etiquetada como **_payable_**. Si tales condiciones no se cumplen, el contrato rechaza cualquier envío de fondos en forma de ethereum." 

Pero lo curioso de esta función son las instrucciones que ordena dentro de su descripción interna:

```solidity
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

Existe una variable de tipo address llamada "*implementation*" (_impl) que solo es tomada en cuenta si es distinta de cero (es decir si ha sido definida) y que se supone, **se trata de otro contrato** y que ejecuta una serie de instrucciones a nivel de **_assembly_**, y que básicamente lo que hace es tomar la data que se le esta instruyendo ejecutar y la esta ejecutando mediante el [comando operacional](https://ethervm.io/) **_DELEGATECALL_**.

Las instrucciones de *Assembly* más o menos están instruyendo lo siguiente:

 - i.- En una variable *ptr* se almacena la data de la función que se desea ejecutar y que se almacena en la memoria *a partir* de la posición 0x40, y la cantidad de datos depende del numero que se recupera de **_calldatasize_**.
 - ii.- En otra variable llamada *result* se **recoge** el resultado de llamar al contrato *implementation* y ejecutar las ordenes que están en ptr, pero, en lugar de hacerlo según el comando **_call_**, **delegatecall** ejecuta todo lo que tiene que hacer como si el contrato que hace las cosas fuera el que le esta invocando y todo cuanto escribe y todo estado que se cambia se asienta en el contrato invocador, y no en el invocado. Esto esta magistralmente explicado en la nota que [CENTRE](https://medium.com/centre-blog/designing-an-upgradeable-ethereum-contract-3d850f637794) preparó especialmente para esta innovadora estrategia, y que sin duda es un avance en el estado del arte de estas tecnologías financieras.
   
   - Lo importante a rescatar aquí es que cuando un contrato invoca la ejecución de una función de otro mediante el comando **delegatecall**, todos los cambios de estado, todo cuanto se escribe, todo fondo que se recibe, llega, se escribe y se actualiza **_en el contrato invocador_**, *no en el invocado*!
   
   ![image](delegatecall.PNG)
   
 - iii.- Esto significa que no es necesario que la función sin nombre devuelva ningún argumento, ya que la orden de assembly *returndatacopy* almacenará en memoria todo lo que haga falta recuperar! (por lo que es innecesario que la función sin nombre devuelva explícitamente argumento alguno, ya que estos quedan alojados en la memoria volátil de la máquina virtual de ethereum y pueden rescatarse mediante la interfaz que este utilizando el usuario).

Todo esto significa que si se ejecuta una función de reemplazo de *implementation*; sin que  nadie siquiera lo note, la lógica que empezará a ejecutar el contrato habrá sido actualizada, sin alterar la data ni los valores de saldo de ninguno de los usuarios!

**N° 7** De modo que para ejecutar correctamente el contrato [True-USD](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#code), lo único que necesitamos es rescatar el ABI de la función de implementación, a no ser que deseemos ejecutar alguna función de control nativa del contrato en cuestión, tal como modificar al contrato de implementación o cambiar al administrador del contrato. El valor de la address del contrato de implementación en el caso de Trust-USD es pública y es [fácilmente rescatable](https://etherscan.io/address/0x0000000000085d4780b73119b644ae5ecd22b376#readContract) como "*implementation*", y su valor es `0xcb9a11afdc6bdb92e4a6235959455f28758b34ba`. Por ende el [ABI](https://etherscan.io/address/0xcb9a11afdc6bdb92e4a6235959455f28758b34ba#code) del contrato de la implementación es fácilmente rescatable (esta vez se trata de una data mucho más extensa):

```cmd
>var abi = [{"constant":true,"inputs":[],"name":"burnMin","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":false,"inputs":[{"name":"_spender","type":"address"},{"name":"_value","type":"uint256"}],"name":"approve","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"owner","type":"address"},{"name":"spender","type":"address"}],"name":"delegateAllowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_minimumGasPriceForFutureRefunds","type":"uint256"}],"name":"setMinimumGasPriceForFutureRefunds","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_from","type":"address"},{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[],"name":"sponsorGas","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateApprove","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_ownable","type":"address"}],"name":"reclaimContract","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"rounding","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":true,"inputs":[],"name":"minimumGasPriceForFutureRefunds","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"mint","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_value","type":"uint256"}],"name":"burn","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"who","type":"address"}],"name":"delegateBalanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"from","type":"address"},{"name":"to","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateTransferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[],"name":"claimOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_min","type":"uint256"},{"name":"_max","type":"uint256"}],"name":"setBurnBounds","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"addedValue","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateIncreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"minimumGasPriceForRefund","outputs":[{"name":"result","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"burnMax","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"paused","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":false,"inputs":[{"name":"_spender","type":"address"},{"name":"_subtractedValue","type":"uint256"}],"name":"decreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_who","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"delegateTotalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"registry","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"remainingGasRefundPool","outputs":[{"name":"length","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"token","type":"address"},{"name":"_to","type":"address"}],"name":"reclaimToken","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"subtractedValue","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateDecreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"symbol","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"}],"name":"reclaimEther","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"to","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateTransfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_registry","type":"address"}],"name":"setRegistry","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_account","type":"address"}],"name":"wipeBlacklistedAccount","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[],"name":"sponsorGas2","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_spender","type":"address"},{"name":"_addedValue","type":"uint256"}],"name":"increaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_who","type":"address"},{"name":"_spender","type":"address"}],"name":"allowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"pendingOwner","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"_index","type":"uint256"}],"name":"gasRefundPool","outputs":[{"name":"gasPrice","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"newOwner","type":"address"}],"name":"transferOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_who","type":"address"},{"name":"_attribute","type":"bytes32"},{"name":"_value","type":"uint256"}],"name":"syncAttributeValue","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"account","type":"address"},{"indexed":false,"name":"balance","type":"uint256"}],"name":"WipeBlacklistedAccount","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"registry","type":"address"}],"name":"SetRegistry","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"name":"newMin","type":"uint256"},{"indexed":false,"name":"newMax","type":"uint256"}],"name":"SetBurnBounds","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"burner","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Burn","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Mint","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"owner","type":"address"},{"indexed":true,"name":"spender","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"from","type":"address"},{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"previousOwner","type":"address"},{"indexed":true,"name":"newOwner","type":"address"}],"name":"OwnershipTransferred","type":"event"}]
undefined 
>_
```

Y ahora al inspeccionar al ABI se obtiene

```cmd
>abi 
```

![image](abi1.PNG)	![image](abi2.PNG)

En donde se observan las funciones mas *comunes* de un token ERC20.

Ahora, si es posible crear la instancia adecuada del contrato:

```cmd
>var interfaz = '0xcb9a11afdc6bdb92e4a6235959455f28758b34ba'
undefined 
>var token = '0x0000000000085d4780B73119b644AE5ecd22b376'
undefined 
>var contrato = new web3.eth.Contract(abi, token)
undefined 
>_
```

Y se pueden obtener los *métodos* ejecutables desde el token mediante la interfaz:

```cmd
>contrato.methods
```
<br>
Existe otro modo de hacer esto mismo utilizando [MyEtherWallet](https://www.myetherwallet.com/access-my-wallet) donde al iniciar la cartera con Metamask, vamos a la sección contratos, colocamos la address del token y en la sección del ABI, colocamos el .json de la implementación (cuidando de haber elegido la red correcta, en este caso MAIN):


#### Estableciendo los parámetros del contrato (*address* y la interfaz *ABI*):

![image](MyEth1.PNG)


#### Visualizando la función "*name*" (sin argumentos requeridos):

![image](MyEth2.PNG)


#### Visualizando la función "*totalSupply*" (sin argumentos requeridos):

![image](MyEth3.PNG)


#### Visualizando la función *decimals* (sin argumentos requeridos):

![image](MyEth4.PNG)


#### Visualizando la función *symbol* (sin argumentos requeridos):

![image](MyEth5.PNG)


#### Visualizando la función *balanceOf* (con la address `0x8bb38C74B8aaf929201f013C9ECc42b750E562c6` como argumento):

![image](MyEth7.PNG)


Desde la consola pueden intentarse métodos similares:

```cmd
>contrato.methods.name().call(function(err, result) { console.log(result) })
Promise { <pending> }
> TrueUSD
```

```cmd
>contrato.methods.totalSupply().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 144003798740000000000000000
``` 

La razón de pasar los resultados de call() a una función e imprimir estos resultados en consola, es debido a la asincronicidad que se experimenta en web3.eth al hacer solicitudes, la soporta JavaScript.

```cmd
>contrato.methods.decimals().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 18
``` 

```cmd
>contrato.methods.symbol().call(function(err, result) { console.log(result) })
Promise { <pending> }
> TUSD
``` 

Como ejercicio, observemos que si hubiésemos utilizado como contrato a la interfaz, aún cuando esta tiene legítimamente el código ABI que hemos ejecutado, encontraremos que sobre este contrato apenas si se ha escrito algo. Es decir, tiene un totalSupply de cero, nadie tiene saldos, etc.

```cmd
>var contrato = new web3.eth.Contract(abi, interfaz)
undefined
>contrato.methods.totalSupply().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 0
``` 

```cmd
>contrato.methods.balanceOf('0x8bb38C74B8aaf929201f013C9ECc42b750E562c6').call(function(err, result) { console.log(result) })
Promise { <pending> }
> 0
``` 

**N° 8** Antes de cerrar el caso interesante de Trust-Coin, debe decirse que estos emprendedores fueron pioneros en la estrategia de la actualización de la plataforma. Su primer plan era primitivo, y era el de copiar los datos a un nuevo contrato, conforme se fueran movilizando los tokens. De este modo quedo para la posteridad un contrato "[Legacy](https://etherscan.io/address/0x8dd5fbCe2F6a956C3022bA3663759011Dd51e73E#code)", contra el cual aun hoy y de manera indefinida se podrán seguir realizando transacciones del token de manera totalmente normal.

Si repetimos los anteriores ejercicios con legacy encontraremos que este contrato conserva su data como un espejo perfecto de la data del contrato activo oficial:

```cmd
>var legacy = '0x8dd5fbCe2F6a956C3022bA3663759011Dd51e73E'
undefined
>_
``` 

Desde luego hay que sustituir el ABI por el que corresponde originalmente a este contrato:

```cmd
>var abi = [{"constant":true,"inputs":[],"name":"burnMin","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"value","type":"uint256"}],"name":"approve","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"owner","type":"address"},{"name":"spender","type":"address"}],"name":"delegateAllowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"burnFeeFlat","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_canReceiveMintWhiteList","type":"address"},{"name":"_canBurnWhiteList","type":"address"},{"name":"_blackList","type":"address"},{"name":"_noFeesList","type":"address"}],"name":"setLists","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"token","type":"address"}],"name":"reclaimToken","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"newContract","type":"address"}],"name":"delegateToNewContract","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_transferFeeNumerator","type":"uint80"},{"name":"_transferFeeDenominator","type":"uint80"},{"name":"_mintFeeNumerator","type":"uint80"},{"name":"_mintFeeDenominator","type":"uint80"},{"name":"_mintFeeFlat","type":"uint256"},{"name":"_burnFeeNumerator","type":"uint80"},{"name":"_burnFeeDenominator","type":"uint80"},{"name":"_burnFeeFlat","type":"uint256"}],"name":"changeStakingFees","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"canReceiveMintWhiteList","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"from","type":"address"},{"name":"to","type":"address"},{"name":"value","type":"uint256"}],"name":"transferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"delegatedFrom","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateApprove","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"contractAddr","type":"address"}],"name":"reclaimContract","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"allowances","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"unpause","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_amount","type":"uint256"}],"name":"mint","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_value","type":"uint256"}],"name":"burn","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"who","type":"address"}],"name":"delegateBalanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"from","type":"address"},{"name":"to","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateTransferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[],"name":"claimOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"sheet","type":"address"}],"name":"setBalanceSheet","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"addedValue","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateIncreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"burnFeeNumerator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"canBurnWhiteList","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"burnMax","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"paused","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"mintFeeDenominator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"staker","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"addr","type":"address"}],"name":"setDelegatedFrom","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"subtractedValue","type":"uint256"}],"name":"decreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"noFeesList","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"who","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"newMin","type":"uint256"},{"name":"newMax","type":"uint256"}],"name":"changeBurnBounds","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"delegateTotalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"balances","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"pause","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"string"},{"name":"_symbol","type":"string"}],"name":"changeName","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"mintFeeNumerator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"transferFeeNumerator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"subtractedValue","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateDecreaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"symbol","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"to","type":"address"},{"name":"value","type":"uint256"},{"name":"origSender","type":"address"}],"name":"delegateTransfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[],"name":"reclaimEther","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"to","type":"address"},{"name":"value","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"newStaker","type":"address"}],"name":"changeStaker","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"account","type":"address"}],"name":"wipeBlacklistedAccount","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"from_","type":"address"},{"name":"value_","type":"uint256"},{"name":"data_","type":"bytes"}],"name":"tokenFallback","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"burnFeeDenominator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"delegate","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"blackList","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"transferFeeDenominator","outputs":[{"name":"","type":"uint80"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"mintFeeFlat","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"addedValue","type":"uint256"}],"name":"increaseApproval","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_owner","type":"address"},{"name":"spender","type":"address"}],"name":"allowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"pendingOwner","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"sheet","type":"address"}],"name":"setAllowanceSheet","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"newOwner","type":"address"}],"name":"transferOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"inputs":[],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"payable":false,"stateMutability":"nonpayable","type":"fallback"},{"anonymous":false,"inputs":[{"indexed":false,"name":"newMin","type":"uint256"},{"indexed":false,"name":"newMax","type":"uint256"}],"name":"ChangeBurnBoundsEvent","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"amount","type":"uint256"}],"name":"Mint","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"account","type":"address"},{"indexed":false,"name":"balance","type":"uint256"}],"name":"WipedAccount","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"newContract","type":"address"}],"name":"DelegatedTo","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"burner","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Burn","type":"event"},{"anonymous":false,"inputs":[],"name":"Pause","type":"event"},{"anonymous":false,"inputs":[],"name":"Unpause","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"previousOwner","type":"address"},{"indexed":true,"name":"newOwner","type":"address"}],"name":"OwnershipTransferred","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"owner","type":"address"},{"indexed":true,"name":"spender","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"from","type":"address"},{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Transfer","type":"event"}]
undefined
>_
``` 

```cmd
>var contrato = new web3.eth.Contract(abi, interfaz)
undefined
>contrato.methods.totalSupply().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 144003798740000000000000000
```

De acuerdo a Etherscan, la exchange Binance posee para el momento que se realiza esta consulta:

![image](Binance.PNG)

De acuerdo a legacy:

```cmd
>contrato.methods.balanceOf('0xF977814e90dA44bFA03b6295A0616a897441aceC').call(function(err, result) { console.log(result) })
Promise { <pending> }
> 42000000000000000000000000
``` 

### II.- El Caso de [USD-Coin](https://etherscan.io/address/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48#code)

Pasemos a un caso mucho más evolucionado en la estrategia de actualizaciones. Este equipo de desarrolladores ya había aprendido las duras lecciones de sus predecesores y construyó un contrato mucho más simple, compacto y versátil.

```cmd
>var token = '0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48'
undefined 
>_
```

Pero para descubrir ahora el ABI, necesitamos la interfaz, que este equipo tuvo la astucia de mantener "*internal*", una variable privada. Aunque ciertamente, nada en la blockchain de ethereum esta oculto, hay modos de complicarle la vida a los advenedizos y curiosos.

En este caso, el contrato que invoca al token USD-Coin se denomina "FiatTokenProxy", y su "*Fallback-Function*", hace una llamada indirecta a una función interna (privada) que es la que contiene el código assembly, un poco mejor diseñado en cuanto a eficiencia (gracias también a los desarrolladores de [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-labs/blob/master/initializer_contracts_with_args/contracts/Proxy.sol) ) y que contiene lo siguiente:

```solidity
  function () payable external {
    _fallback();
  }
  
  function _fallback() internal {
    _willFallback();
    _delegate(_implementation());
  }
  
  function _willFallback() internal {
  }
  
  function _implementation() internal view returns (address impl) {
    bytes32 slot = IMPLEMENTATION_SLOT;
    assembly {
      impl := sload(slot)
    }
  }

  function _delegate(address implementation) internal {
    assembly {
      // Copy msg.data. We take full control of memory in this inline assembly
      // block because it will not return to Solidity code. We overwrite the
      // Solidity scratch pad at memory position 0.
      calldatacopy(0, 0, calldatasize)

      // Call the implementation.
      // out and outsize are 0 because we don't know the size yet.
      let result := delegatecall(gas, implementation, 0, calldatasize, 0, 0)

      // Copy the returned data.
      returndatacopy(0, 0, returndatasize)

      switch result
      case 0 { revert(0, returndatasize) }
      default { return(0, returndatasize) }
    }
  }
```

Que esencialmente hace lo mismo que en el caso de Trust-Token. Afortunadamente el valor de la address del contrato de implementación fue invocado en la función constructora del contrato y hay que admitirlo, pudieron complicarle mucho más las cosas a los curiosos novatos. Esta address de la implementación es:

```cmd
>var implementación = '0x0882477e7895bdc5cea7cb1552ed914ab157fe56'
undefined 
>_
```

No solo eso: el equipo de Centre tuvo la gentileza de verificar el contrato de implementación, y su ABI es:

```cmd
>var abi = [{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_spender","type":"address"},{"name":"_value","type":"uint256"}],"name":"approve","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_account","type":"address"}],"name":"unBlacklist","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_from","type":"address"},{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"minter","type":"address"}],"name":"removeMinter","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"string"},{"name":"_symbol","type":"string"},{"name":"_currency","type":"string"},{"name":"_decimals","type":"uint8"},{"name":"_masterMinter","type":"address"},{"name":"_pauser","type":"address"},{"name":"_blacklister","type":"address"},{"name":"_owner","type":"address"}],"name":"initialize","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"masterMinter","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"unpause","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_amount","type":"uint256"}],"name":"mint","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_amount","type":"uint256"}],"name":"burn","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"minter","type":"address"},{"name":"minterAllowedAmount","type":"uint256"}],"name":"configureMinter","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_newPauser","type":"address"}],"name":"updatePauser","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"paused","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"account","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"pause","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"minter","type":"address"}],"name":"minterAllowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"symbol","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"pauser","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_newMasterMinter","type":"address"}],"name":"updateMasterMinter","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"account","type":"address"}],"name":"isMinter","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_newBlacklister","type":"address"}],"name":"updateBlacklister","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"blacklister","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"owner","type":"address"},{"name":"spender","type":"address"}],"name":"allowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"currency","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"newOwner","type":"address"}],"name":"transferOwnership","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_account","type":"address"}],"name":"blacklist","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_account","type":"address"}],"name":"isBlacklisted","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"minter","type":"address"},{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"amount","type":"uint256"}],"name":"Mint","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"burner","type":"address"},{"indexed":false,"name":"amount","type":"uint256"}],"name":"Burn","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"minter","type":"address"},{"indexed":false,"name":"minterAllowedAmount","type":"uint256"}],"name":"MinterConfigured","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"oldMinter","type":"address"}],"name":"MinterRemoved","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"newMasterMinter","type":"address"}],"name":"MasterMinterChanged","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_account","type":"address"}],"name":"Blacklisted","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_account","type":"address"}],"name":"UnBlacklisted","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"newBlacklister","type":"address"}],"name":"BlacklisterChanged","type":"event"},{"anonymous":false,"inputs":[],"name":"Pause","type":"event"},{"anonymous":false,"inputs":[],"name":"Unpause","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"newAddress","type":"address"}],"name":"PauserChanged","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"owner","type":"address"},{"indexed":true,"name":"spender","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"name":"previousOwner","type":"address"},{"indexed":false,"name":"newOwner","type":"address"}],"name":"OwnershipTransferred","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"from","type":"address"},{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Transfer","type":"event"}]
undefined 
>_
```

De tal modo que ahora podríamos jugar con el contrato usando web3:

```cmd
>var contrato = new web3.eth.Contract(abi, token)
undefined 
>contrato.methods.name().call(function(err, result) { console.log(result) })
Promise { <pending> }
> USD//C
```

```cmd
>contrato.methods.totalSupply().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 444815302970000
```

```cmd
>contrato.methods.decimals().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 6
```

```cmd
>contrato.methods.balanceOf('0xA910f92ACdAf488fa6eF02174fb86208Ad7722ba').call(function(err, result) { console.log(result) })
Promise { <pending> }
> 8001035943712
```

![image](Poloniex.PNG)

Contrario al caso de Trust Token, el contrato de implementación de USD-Coin esta totalmente vacío, literalmente funge como una especie de librería del contrato principal:

```cmd
>var contrato = new web3.eth.Contract(abi, implementación)
undefined 
>contrato.methods.name().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 
```

```cmd
>contrato.methods.totalSupply().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 0
```

```cmd
>contrato.methods.decimals().call(function(err, result) { console.log(result) })
Promise { <pending> }
> 0
```

```cmd
>contrato.methods.balanceOf('0xA910f92ACdAf488fa6eF02174fb86208Ad7722ba').call(function(err, result) { console.log(result) })
Promise { <pending> }
> 0
```

### III.- El Caso de [EURS-Stasis](https://etherscan.io/address/0xdb25f211ab05b1c97d595516f45794528a807ad8#code)

Finalmente abordaremos el caso de EURS - Stasis. Este contrato que posee un plan de actualización con tecnología intermedia, introduce una gran innovación: la transferencia con delegación por firmas; la cual tendría la capacidad incidentalmente de resolver los inconvenientes que generan implícitamente las funciones "*approve*" y "*transferFrom*", haciendo de ellas (junto con su mapeo "*allowances*") objetos de programación obsoletos en un token ERC20.

N° 1 **Estrategia de Actualización**: En el contrato existe un modificador llamado "*delegatable*", que afecta a todas las funciones importantes del contrato. Asimismo, el contrato posee una función sin nombre (*fallback*) que lógicamente es afectada por el modificador, en caso que el contrato al que apunta la delegación, posea funciones cuyos nombres sean nuevos y no hayan sido previstos en el contrato actual.
 
Asimismo, el contrato posee una variable "**_delegate_**", la cual es la dirección de la delegación o el contrato delegado. Mientras que esta variable conserva su valor inicial (que por defecto es `address(0)`) la función sin nombre, de llegar a invocarse siempre abortará su ejecución; y todas las funciones del contrato que invocan el modificador, se comportarán justo como lo indica textualmente el contrato en cuestión.

**_delegate_** es una variable "*internal*", lo que quiere decir que no es visible al público, ni hay un modo directo de consultarla.

El modificador **_delegatable_** posee las siguientes instrucciones:

```solidity
  modifier delegatable {
    if (delegate == address (0)) {
      require (msg.value == 0); // Non payable if not delegated
      _;
    } else {
      assembly {
        // Save owner
        let oldOwner := sload (owner_slot)

        // Save delegate
        let oldDelegate := sload (delegate_slot)

        // Solidity stores address of the beginning of free memory at 0x40
        let buffer := mload (0x40)

        // Copy message call data into buffer
        calldatacopy (buffer, 0, calldatasize)

        // Lets call our delegate
        let result := delegatecall (gas, oldDelegate, buffer, calldatasize, buffer, 0)

        // Check, whether owner was changed
        switch eq (oldOwner, sload (owner_slot))
        case 1 {} // Owner was not changed, fine
        default {revert (0, 0) } // Owner was changed, revert!

        // Check, whether delegate was changed
        switch eq (oldDelegate, sload (delegate_slot))
        case 1 {} // Delegate was not changed, fine
        default {revert (0, 0) } // Delegate was changed, revert!

        // Copy returned value into buffer
        returndatacopy (buffer, 0, returndatasize)

        // Check call status
        switch result
        case 0 { revert (buffer, returndatasize) } // Call failed, revert!
        default { return (buffer, returndatasize) } // Call succeeded, return
      }
    }
  }

```

Básicamente lo que instruye este modificador es que si el valor de *delegate* es `address(0)` (Y, ADEMAS, que si el valor de ethers enviados al contrato es nulo) que se continúe la ejecución de las funciones del contrato tal y como se indica en el mismo contrato, pero de no ser así, se ejecutará de manera delegada las ordenes que se instruyan en la data, contra el contrato que está en la address *delegate*; pero (tal como se indica en los bucles de *switch* para *owner* y *delegate*), si en el proceso de ejecución delegada, alguno de estos parámetros fue modificado, entonces se ordena abortar toda la transacción. Finalmente (la ultima instancia de *switch*) si la ejecución delegada no devuelve datos, se entiende que es una transacción fallida y se ordena revertir.  

N° 2 **Valor Actual de _delegate_**: Dado que no es posible hacer una consulta directa sobre una variable interna o privada de un contrato, es necesario realizar una detección de todos los posibles eventos vinculados con el cambio de este parámetro en el contrato. Afortunadamente el equipo de [STASIS](https://stasis.net/) tuvo la generosidad de prever un evento asociado al cambio de este parámetro, el cual será emitido cada vez que tal cosa ocurra; el evento **_Delegation_**. Otros parámetros internos al cambiar, lo hacen *silenciosamente*, tal como los casos de las funciones **_setOwner_** y **_setFeeCollector_**. Detectar en la blockchain semejantes cambios silenciosos requiere de escuchar atentamente al contrato, transacción por transacción desde el número de bloque en que fue creado hasta el más reciente. Es un desafío no convencional, y dependiendo del tráfico que experimenta ese contrato, se trata de un desafío casi imposible manualmente, a no ser que se desarrolle un código especial para ello.
 
¿Cómo *escuchar* un determinado evento, emitido por un contrato y rastrear el anuncio de los cambios a lo largo de la blockchain?: mediante un método del paquete [web3.eth.Contract](https://web3js.readthedocs.io/en/v1.2.4/web3-eth-contract.html#web3-eth-contract), el método [**_getPastEvents_**](https://web3js.readthedocs.io/en/v1.2.4/web3-eth-contract.html#getpastevents).

Afortunadamente existe un método sencillo de programar la ejecución de este rastreador de eventos, usando JavaScript mediante una página web como interfaz de usuario, o bien utilizando el entorno de ejecución de Node.js.

En este caso, dado que ya nos hemos apoyado en Node, se sugiere un pequeño código JavaScript, para ejecutarlo en Node. Se sugiere crear un directorio para este propósito, por ejemplo `C:\Users\MiUsuario\CarpetaLocal\Delegate`; en esta carpeta crearemos el siguiente archivo JavaScript (por ejemplo `app.js`):

```js
const Web3 = require('web3');
const INFURA_KEY = '<la llave de su proyecto INFURA>';
const url = 'https://mainnet.infura.io/v3/'+INFURA_KEY;
const web3 = new Web3(url);
const abi = [{"constant":false,"inputs":[],"name":"freezeTransfers","outputs":[],"payable":true,"stateMutability":"payable","type":"function"},{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_spender","type":"address"},{"name":"_value","type":"uint256"}],"name":"approve","outputs":[{"name":"success","type":"bool"}],"payable":true,"stateMutability":"payable","type":"function"},{"constant":false,"inputs":[{"name":"_newOwner","type":"address"}],"name":"setOwner","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_from","type":"address"},{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transferFrom","outputs":[{"name":"","type":"bool"}],"payable":true,"stateMutability":"payable","type":"function"},{"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"unfreezeTransfers","outputs":[],"payable":true,"stateMutability":"payable","type":"function"},{"constant":true,"inputs":[],"name":"getFeeParameters","outputs":[{"name":"_fixedFee","type":"uint256"},{"name":"_minVariableFee","type":"uint256"},{"name":"_maxVariableFee","type":"uint256"},{"name":"_variableFeeNumnerator","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_value","type":"uint256"}],"name":"burnTokens","outputs":[{"name":"","type":"bool"}],"payable":true,"stateMutability":"payable","type":"function"},{"constant":true,"inputs":[{"name":"_owner","type":"address"}],"name":"balanceOf","outputs":[{"name":"balance","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"_owner","type":"address"}],"name":"nonce","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_value","type":"uint256"}],"name":"createTokens","outputs":[{"name":"","type":"bool"}],"payable":true,"stateMutability":"payable","type":"function"},{"constant":true,"inputs":[],"name":"symbol","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"_amount","type":"uint256"}],"name":"calculateFee","outputs":[{"name":"_fee","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"_address","type":"address"}],"name":"flags","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_newFeeCollector","type":"address"}],"name":"setFeeCollector","outputs":[],"payable":true,"stateMutability":"payable","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"payable":true,"stateMutability":"payable","type":"function"},{"constant":false,"inputs":[{"name":"_address","type":"address"},{"name":"_flags","type":"uint256"}],"name":"setFlags","outputs":[],"payable":true,"stateMutability":"payable","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_value","type":"uint256"},{"name":"_fee","type":"uint256"},{"name":"_nonce","type":"uint256"},{"name":"_v","type":"uint8"},{"name":"_r","type":"bytes32"},{"name":"_s","type":"bytes32"}],"name":"delegatedTransfer","outputs":[{"name":"","type":"bool"}],"payable":true,"stateMutability":"payable","type":"function"},{"constant":false,"inputs":[{"name":"_delegate","type":"address"}],"name":"setDelegate","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_fixedFee","type":"uint256"},{"name":"_minVariableFee","type":"uint256"},{"name":"_maxVariableFee","type":"uint256"},{"name":"_variableFeeNumerator","type":"uint256"}],"name":"setFeeParameters","outputs":[],"payable":true,"stateMutability":"payable","type":"function"},{"constant":true,"inputs":[{"name":"_owner","type":"address"},{"name":"_spender","type":"address"}],"name":"allowance","outputs":[{"name":"remaining","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"inputs":[{"name":"_feeCollector","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"payable":true,"stateMutability":"payable","type":"fallback"},{"anonymous":false,"inputs":[],"name":"Freeze","type":"event"},{"anonymous":false,"inputs":[],"name":"Unfreeze","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"name":"fixedFee","type":"uint256"},{"indexed":false,"name":"minVariableFee","type":"uint256"},{"indexed":false,"name":"maxVariableFee","type":"uint256"},{"indexed":false,"name":"variableFeeNumerator","type":"uint256"}],"name":"FeeChange","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"name":"delegate","type":"address"}],"name":"Delegation","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_from","type":"address"},{"indexed":true,"name":"_to","type":"address"},{"indexed":false,"name":"_value","type":"uint256"}],"name":"Transfer","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_owner","type":"address"},{"indexed":true,"name":"_spender","type":"address"},{"indexed":false,"name":"_value","type":"uint256"}],"name":"Approval","type":"event"}];
const EURS = '0xdB25f211AB05b1c97D595516F45794528a807ad8';
const EursToken = new web3.eth.Contract(abi, EURS);
EursToken.getPastEvents(
  'Delegation',
  {
    fromBlock: 5835251,
    toBlock: 'latest'
  },
  (error, evento) => { console.log(evento) }
)

```

Finalmente, desde la consola de comandos ejecutamos:

```cmd
C:\Users\MiUsuario\CarpetaLocal\Delegate >node app.js

```

Lo cual nos retorna la extraña respuesta de:

```cmd
C:\Users\MiUsuario\CarpetaLocal\Delegate > 
[]
```

Esto significa que (al menos a la fecha en que esta inspección se realizó, el 25 de enero de 2020) la respuesta nos devuelve un arreglo que esta vacío. Es decir, que nunca se ha ejecutado la función *setDelegate* desde el día que el contrato fue desplegado, si quiera una vez. De lo que se deduce que el valor del parámetro *delegate* es `address(0)`. Nótese que la función *setDelegate* sólo puede ejecutarla el propietario del contrato, debido a la instrucción: `require (msg.sender == owner);` que el contrato contiene en su línea 776.

Podemos estar seguros que se trata de un arreglo vacío al contrastar el caso con el parámetro que devuelve el rastreo del evento *FeeChange* que se emite cada vez que se ejecuta la función *setFeeParameters* (igualmente restringida sólo al dueño del contrato). Por ejemplo, si creamos un nuevo archivo JavaScript, por ejemplo `fee.js` cambiando la última instrucción respecto de `app.js` por:

```js

EursToken.getPastEvents(
  'FeeChange',
  {
    fromBlock: 5835251,
    toBlock: 'latest'
  },
  (error, evento) => { console.log(evento) }
)

```

Al ejecutar:

```cmd
C:\Users\MiUsuario\CarpetaLocal\Delegate >node fee

```

Se obtiene el siguiente resultado:

```js
C:\Users\MiUsuario\CarpetaLocal\Delegate > 
[
  {
    address: '0xdB25f211AB05b1c97D595516F45794528a807ad8',
    blockHash: '0xbf9eea602be3959afc5e7cae404dc5d16c10f64f9f7a4b490fb6887cc6fc1edf',
    blockNumber: 5835461,
    logIndex: 83,
    removed: false,
    transactionHash: '0xffafe1e2aae3fd207ca5d15a7aed2f5498b3062f54cefa5fce05e597c8b08407',
    transactionIndex: 158,
    id: 'log_19305eb1',
    returnValues: Result {
      '0': '50',
      '1': '0',
      '2': '0',
      '3': '0',
      fixedFee: '50',
      minVariableFee: '0',
      maxVariableFee: '0',
      variableFeeNumerator: '0'
    },
    event: 'FeeChange',
    signature: '0x650bf5314bb5924368ffebaf7dffcfaa4a0f99c2ab08264c26bf0547f8c459e9',
    raw: {
      data: '0x0000000000000000000000000000000000000000000000000000000000000032000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
      topics: [Array]
    }
  },
  {
    address: '0xdB25f211AB05b1c97D595516F45794528a807ad8',
    blockHash: '0xcd33aaa4fd037ad1c95939b7ac4a4980a11b7a90bf7a11a40ddf3071e2a60ff4',
    blockNumber: 8010430,
    logIndex: 8,
    removed: false,
    transactionHash: '0x427b89f50d289143e1381a225366d1a36f41637a3c8290dd1057fa941fd0d436',
    transactionIndex: 14,
    id: 'log_d07a5910',
    returnValues: Result {
      '0': '0',
      '1': '0',
      '2': '0',
      '3': '0',
      fixedFee: '0',
      minVariableFee: '0',
      maxVariableFee: '0',
      variableFeeNumerator: '0'
    },
    event: 'FeeChange',
    signature: '0x650bf5314bb5924368ffebaf7dffcfaa4a0f99c2ab08264c26bf0547f8c459e9',
    raw: {
      data: '0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
      topics: [Array]
    }
  }
]

```

Lo cual advierte que apenas 1 hora después de desplegarse el contrato (en el bloque N° `5835461`, el 22 de junio de 2018), se establecieron tarifas de comisión por ejecutar transferencias delegadas. Justamente un año después (en el bloque N° `8010430`, el 22 de junio de 2019), el equipo de STASIS toma la decisión de establecer a cero todas las comisiones. De como logran tal hazaña, ya es parte de una estrategia de negocios que escapa del alcance de este análisis.

Es de notar que las transacciones que provocan la emisión de los eventos *FeeChange*, se originan de la invocación de un contrato diferente a **EURSToken**: Un contrato [**_Wallet_**](https://etherscan.io/address/0x2ebbbc541e8f8f24386fa319c79ceda0579f1efb#code), el cual ejecuta una transacción genérica: *confirm*; la cual invoca el valor de un mapa con una pre-imagen igual a un valor hash cuya imagen son los datos de una cierta transacción (que se borran tras le ejecución), que a su vez puede llamar a otro u otros contratos. *Wallet* es un tipo de contrato para manejar transacciones de manera indirecta y protegida, por parte de un grupo de propietarios, de tal modo de ofuscar los datos de tales transacciones y exigir autorización apropiada para cada transacción de interés.

10.3 **La Innovación de la Función _delegatedTransfer_** : Muchas veces la innovación consiste en cambios sencillos que no se habían tomado en serio con anterioridad, pero cuando se implementan, destacan cuan relevantes son para la adopción de una tecnología.

Si la intención de usar monedas estables, es una adopción que de algún modo irrumpa la barrera del efecto de red, necesitamos que el usuario final pueda utilizar esta tecnología sin requerir de él conocimientos avanzados en lo que está sucediendo dentro de su aplicación. Y una de esas barreras es la necesidad de pagar comisiones de *gasolina* en moneda ethereum, para poder movilizar fondos que existen en otra moneda totalmente diferente: euros y dólares!

¿Como le explicamos al ciudadano común que ésos dólares que desea enviar, no se moverán de su cartera a no ser que adquiera y coloque en su misma "*address*" una cantidad suficiente de *ethers*? Es decir, no bastó toda la travesía de adquirir los *Tokens* en moneda estable (ya sean dólares o euros), además debe hacer otra compleja tramitación para adquirir una criptomoneda, que en principio ni le interesa o no le tiene porque interesar para nada. Y todo esto para comprar una revista, un café o pagar su factura de electricidad.

Este es el aporte tecnológico de la función **_delegatedTransfer_** cuya misión es evitarle al usuario final la necesidad de lidiar con una criptomoneda, cuando su campo de acción se concentra en una moneda diferente. El equipo de STASIS desarrollo una aplicación, disponible tanto para móviles con sistema operativo [**_Android_**](https://play.google.com/store/apps/details?id=com.stasis.stasiswallet), como móviles [**_iOS_**](https://apps.apple.com/app/stasis-wallet/id1371949230) que le permiten al usuario enviar a un *delegado* una solicitud de movilización de fondos, mediante comunicación cliente-servidor totalmente discriminada de la blockchain (una llamada RPC off-chain), para poder llevar a cabo los movimientos del EURS-token. Esta solicitud viene acompañada de una firma criptográfica [ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm) bajo el [estándar Ethereum](https://ethereum.stackexchange.com/questions/64380/understanding-ethereum-signatures) que comprende tres parámetros: dos cadenas de 32 bytes llamadas "*R*" y "*S*" y un número de un byte de extensión, o parámetro "*V*".

Dado que este tema se desvía ya del objetivo de este análisis de las estrategias para la actualización de contratos utilizados por algunas monedas estables, a continuación le doy el análisis más breve posible a la función _delegatedTransfer_:

**i.- Cómo funciona _delegatedTransfer_**. El código solidity de esta función es:

```solidity

function delegatedTransfer (
    address _to, uint256 _value, uint256 _fee,
    uint256 _nonce, uint8 _v, bytes32 _r, bytes32 _s)
  public delegatable payable returns (bool) {
    if (frozen) return false;
    else {
      address _from = ecrecover (
        keccak256 (
          thisAddress (), messageSenderAddress (), _to, _value, _fee, _nonce),
        _v, _r, _s);

      if (_nonce != nonces [_from]) return false;

      if (
        (addressFlags [_from] | addressFlags [_to]) & BLACK_LIST_FLAG ==
        BLACK_LIST_FLAG)
        return false;

      uint256 fee =
        (addressFlags [_from] | addressFlags [_to]) & ZERO_FEE_FLAG == ZERO_FEE_FLAG ?
          0 :
          calculateFee (_value);

      uint256 balance = accounts [_from];
      if (_value > balance) return false;
      balance = safeSub (balance, _value);
      if (fee > balance) return false;
      balance = safeSub (balance, fee);
      if (_fee > balance) return false;
      balance = safeSub (balance, _fee);

      nonces [_from] = _nonce + 1;

      accounts [_from] = balance;
      accounts [_to] = safeAdd (accounts [_to], _value);
      accounts [feeCollector] = safeAdd (accounts [feeCollector], fee);
      accounts [msg.sender] = safeAdd (accounts [msg.sender], _fee);

      Transfer (_from, _to, _value);
      Transfer (_from, feeCollector, fee);
      Transfer (_from, msg.sender, _fee);

      return true;
    }
  }

```

La función toma siete (7) parámetros, de los cuales sólo cuatro (4) de ellos son variables del entorno del contrato y las otras tres (3) constituyen simplemente la firma ECDSA, con los parámetros v (uint8), r (bytes32) y s (bytes32). Las cuatro primeras variables son:

 **\_to** (tipo de variable: **_address_**): Es la dirección hacia donde serán transferidos los fondos.<br>
 **\_value** (**_uint256_**): Cantidad de fondos a ser transferidos.<br>
 **\_fee** (**_uint256_**): Comisión a ser pagada al "*delegado*".<br>
 **\_nonce** (**_uint256_**): Numero criptográfico de uso único. <br>
El nonce es un elemento de seguridad que requieren las firmas ECDSA para prevenir ataques de falsificación. Para este fin el contrato que implementa **_delegatedTransfer_** debe también implementar un mapa que lleva la cuenta de los nonces internos para las addresses que utilizan el contrato. En el caso de EURSToken, este mapa es una variable interna (**_nonces_**) pero es consultable públicamente mediante la función:
 
 ```solidity
 
 function nonce (address _owner) public view delegatable returns (uint256) {
    return nonces [_owner];
  }
 
 ```
 
Finalmente, lo que hace **_delegatedTransfer_** es verificar cuantos fondos posee el signatario del mensaje (la address que originó la firma v,r,s) y confirmar que el nonce aprobado por la firma corresponde con el nonce interno del contrato. En el caso de EURSToken, se verifican otras condiciones propias de ése contrato, como chequear que el signatario no está en ninguna "*lista negra*" de prevención al lavado de capitales o si el contrato no esta pausado.

Si todo está en orden, se procede con las respectivas transferencias de fondos. La cantidad **_\_value_** se le acredita a la cuenta **_\_to_** y la cantidad **_\_fee_** se le acredita a **_msg.sender_** quien quiera que sea, y que perfectamente puede ser un contrato o una cuenta externamente controlada (EOA). Al signatario se le actualiza su saldo con las deducciones de **_\_value_** y **_\_fee_**.


**ii.- Por qué la función "*approve*" no funciona pero _delegatedTransfer_ sí lo hace**. De acuerdo al [estándar ERC20](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md) la [configuración recomendada](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol) para la función **_approve_** es:

```solidity

function approve(address spender, uint256 amount) public returns (bool) {
        _approve(_msgSender(), spender, amount);
        return true;
    }

```

donde 

```solidity

function _approve(address owner, address spender, uint256 amount) internal {
        require(owner != address(0), "ERC20: approve from the zero address");
        require(spender != address(0), "ERC20: approve to the zero address");

        _allowances[owner][spender] = amount;
        emit Approval(owner, spender, amount);
    }

```

por otro lado, *\_allowances* es un mapa que registra autorizaciones o delegaciones de fondos:

```solidity
mapping (address => mapping (address => uint256)) private _allowances;
```

y \_msgSender() es una función que identifica quién esta invocando al contrato:

```solidity

    function _msgSender() internal view returns (address payable) {
        return msg.sender;
    }

```

Lo que en pocas palabras significa que **_approve_** modifica el mapa *\_allowances* asumiendo como dueño de los fondos únicamente a la dirección **_msg.sender_**, es decir, el ente o elemento que hace **_directamente_** la llamada al contrato que maneja los tokens ERC20.

No hay manera de delegar el manejo de fondos a través de un contrato que intermedie en esa transacción; tiene que ser *directamente* el dueño de estos fondos. Adicionalmente, la invocación de approve que necesariamente debe realizarse mediante una transacción es sólo la mitad de la historia: una vez que la cuenta **_spender_** ha sido autorizada, ésta debe efectuar la invocación a la función **_transferFrom_** para en efecto hacer uso (cualquiera que sea) de los fondos. Y esto debe realizarse en otra transacción aparte, lo cual obliga al interesado costear dos veces el monto mínimo de gasolina que exige una transacción (21.000 unidades de Gas).

Además de [otras complicaciones](https://blog.smartdec.net/erc20-approve-issue-in-simple-words-a41aaf47bca6) que pueden surgir, esto ya es difícil de incorporar en una aplicación para usuarios en general, que no necesitan enterarse de las capas internas de la tecnología que usan.

A diferencia de esto, **_delegatedTransfer_** es una función segura de delegación de fondos y al mismo tiempo una asignación efectiva de fondos hacia cualquier entidad (sea contrato o cuenta normal), y por ende puede ser invocada desde un contrato, a través de funciones que pueden invocar otras funciones, de cualquier otra cantidad de contratos que se necesiten **_en una sola transacción_**. Lo que hace posible que el usuario final, ni siquiera tenga que lidiar con la compra de una *criptomoneda* llamada Ethereum y adicionalmente lo que convierte potencialmente a la función **_delegatedTransfer_** en un sustituto idóneo para *approve* y *transferFrom*, dejando obsoletas estas funciones en el estándar ERC20.
