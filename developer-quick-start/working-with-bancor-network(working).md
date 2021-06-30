---
description: How to navigate the Bancor Network contracts
---

# Working with Bancor Network

Bancor Network's system of smart contracts work together to provide a complete solution to on-chain liquidity. We've highlighted some of the key network functions in the `Developer Quick Start` and encourage you to begin your development process here. Down below in the `Other Guides` section, you'll find some deeper dives into various elements of Bancor Network.

The Bancor contract ecosystem contains a number of contracts, and a number of different types of contracts. As a developer, one of the tasks you will need to be able to accomplish is to find the address of desired contracts. The Bancor network includes a contract address discovery mechanism - a contract that servers as a registry for all the core contracts and queries the latest contract addresses by name. These contracts are updated from time to time, so developers are advised to reference the registry directly in order to stay current with the network. 

Each Quick Start guide has code snippets which demonstrate how to call the registry, however we've will also outline the process and the different approaches below.

## Step 1: Identifying the address and ABI of the ContractRegistry

The `ContractRegistry` is the first stop in discovering the address for any contract on the Bancor Network, and discovering the address is usually the first step in being able to perform the action you would like to execute. The address for the `ContractRegistry` can be found below, and also in the [addresses](https://docs.bancor.network/ethereum-contracts/addresses) section of the docs. Alternatively, as of this writing, [Etherscan](https://etherscan.io) labels a number of the Bancor contracts with a "Bancor" label, and the `ContractRegistry` is labelled "Bancor: Contract Registry". If you are looking to interact manually with the contract, this can be done through Etherscan's interface by clicking on the "Contract" tab, and clicking "Read Contract" for read-only functions, and "Write Contract" in order to interact with functions which change the state of the contract.

An ABI is a representation of the contract useful in many contexts. We'll often need it in order to build objects of contracts to interact with through code. The ABI for the registry can be found in the documentation. Alternatively, it can be copied from Etherscan using the address if the Registry (from above). In the "Contract" tab there are options to copy the ABI to the clipboard.

## Step 2: Building a Contract Object

In order to use the `ContractRegistry` in code, you'll need to build a contract object. In order to do this, you'll need:
* the address of the `ContractRegistry` (see above)
* the abi of the `ContractRegistry` (also above)
* a connection to the blockchain - this guide will assume Infura

{% tabs %}
{% tab title="Web3.js" %}
```javascript
const Web3 = require("web3");

const NODE_ADDRESS = "https://mainnet.infura.io/v3/<INFURA_ID>";
const web3 = new Web3(NODE_ADDRESS);

const contractRegistry = new web3.eth.Contract(CONTRACT_REGISTRY_ABI, CONTRACT_REGISTRY_ADDRESS);

console.log(`Contract Registry object connected to Registry at ${CONTRACT_REGISTRY_ADDRESS}`);
```
{% endtab %}

{% tab title="Ethers" %}
```javascript
const { ethers } = require("ethers");

const INFURA_PROJECT_ID = "<INFURA_ID>";
const provider = ethers.getDefaultProvider("mainnet", INFURA_PROJECT_ID);

const contractRegistry = new ethers.Contract(CONTRACT_REGISTRY_ADDRESS, CONTRACT_REGISTRY_ABI, provider);
console.log(`Contract Registry object connected to Registry at ${CONTRACT_REGISTRY_ADDRESS}`);
```
{% endtab %}
{% endtabs %}


## Option 1: Querying the ContractRegistry (recommended)

Now that we know how to interact with the Registry in JavaScript, we can use the Registry to get the addresses of other contracts in the Bancor Network. This is done using the `addressOf` function on the `ContractRegistry`. The `addressOf` function returns contracts by name - a name of a contract is input, and the address is returned. The argument must be formatted as a `bytes32` string - we'll show how to do that in the two most popular JavaScript web3 libraries, web3.js and ethers.js.

For example, to get the latest contract address of the `LiquidityProtection` contract, the following call should be used:

{% tabs %}
{% tab title="Web3.js" %}
```javascript
const liquidityProtectionAddress = await contractRegistry.methods.addressOf(Web3.utils.asciiToHex('LiquidityProtection').call();
```
{% endtab %}

{% tab title="Ethers" %}
```javascript
const liquidityProtectionAddress = await contractRegistry.addressOf(ethers.utils.formatBytes32String('LiquidityProtection'));
```
{% endtab %}
{% endtabs %}


The `addressOf` function on the **ContractRegistry** [contract](https://etherscan.io/address/0x52ae12abe5d8bd778bd5397f99ca900624cfadd4#readContract) can receive any of the known contract names as an argument, in bytes32 format. There is a list of all core contract names at the bottom of this doc, along with some of their bytes32 representations.

## Option 2: Listen to AddressUpdate Events

Every time an address is changed, the `ContractRegistry` will throw an `AddressUpdate` event. An event listener/scraper can be made to listen for these events, updating a store of contract addresses any time one is updated. The structure of the event is:

```text
AddressUpdate (index_topic_1 bytes32 _contractName, address _contractAddress)
```
For example, in order to get the latest contract address of the `BancorNetwork` contract, the event listener/scraper should look for `AddressUpdate` events that indicate new address for `BancorNetwork` \(using its bytes32 representation\). Building an event scraper is out of the scope for this documentation, here a couple of resources [for](https://ethereum.stackexchange.com/a/87698/27270) [ethers](https://docs.ethers.io/v5/api/providers/provider/#Provider-getLogs) and [for](https://ethereum.stackexchange.com/questions/11866/web3-how-do-i-get-past-events-of-mycontract-myevent/102727#102727) [web3.js](https://web3js.readthedocs.io/en/v1.3.4/web3-eth-contract.html#getpastevents).

You can also build a listener using this pattern in order to detect new addresses when the system is updated. (links: ethers: [docs](https://docs.ethers.io/v5/api/contract/contract/#Contract-on), [StackExchange](https://ethereum.stackexchange.com/a/87669/27270), web3.js: [docs](https://web3js.readthedocs.io/en/v1.3.4/web3-eth-contract.html#contract-events), [StackExchange](https://ethereum.stackexchange.com/questions/11866/web3-how-do-i-get-past-events-of-mycontract-myevent/102727#102727))

## Contract Names and Addresses

### ContractRegistry address

| Network | Contract Address |
| :--- | :--- |
| `Mainnet​` | `0x52Ae12ABe5D8BD778BD5397F99cA900624CfADD4` |
| `Ropsten` | `0xA6DB4B0963C37Bc959CbC0a874B5bDDf2250f26F` |

{% hint style="info" %}
ContractRegistry also indicates its own address, which means you should run the same process to make sure you are working with the latest version of the ContractRegistry
{% endhint %}

### List of Contracts Names and Bytes32 Representation

Below is a list of major contracts along with the `bytes32` repersentation of their names. (This can be used to bypass the need for `asciiToHex` or `formatBytes32String`):

| **Contract Name** | bytes32 Representation |
| :--- | :--- |
| `ContractRegistry` | `0x436f6e74726163745265676973747279` |
| `BancorNetwork` | `0x42616e636f724e6574776f726b` |
| `BancorFormula` | `0x42616e636f72466f726d756c61` |
| `BancorConverterRegistry` | `0x42616e636f72436f6e7665727465725265676973747279` |

{% hint style="info" %}
If you are not sure about the contract name or bytes32 representation, you can always trigger the function contractNames with a serial number as an input \(i.e. 0, 1, 2, etc\). The response will be the name of the available contract.
{% endhint %}

In addition, the following contracts can be queried using the `ContractRegistry` by using `asciiToHex` (in web3.js) or `formatBytes32String` on the contract names as written here:

* `ConverterFactory`
* `ConversionPathFinder`
* `ConverterUpgrader`
* `BancorConverterRegistryData`
* `BNTToken`
* `BancorX`
* `BancorXUpgrader`


