---
description: Creating a liquidity pool requires just one transaction as of v0.6
---

# Deploying Liquidity Pools

As of v0.6, the Bancor `ConverterRegistry` contract does most of the hard work deploying new liquidity pools. Developers can still use the manual process to test their converters \(see [`Deploying a Liquidity Pool`](../guides/deploying-a-liquidity-pool-testing.md) in the Guides section\), but the converter won't be discoverable to the rest of Bancor Network.

{% hint style="info" %}
The v0.6 release will go live in mid June. Until then, please refer to the **Deploying a Liquidity Pool for Testing** guide found [**here**](https://docs.bancor.network/guides/deploying-a-liquidity-pool-testing). The process described will be deprecated to a testing-only function once v0.6 is live.
{% endhint %}

{% hint style="info" %}
This guide is for developers integrating Bancor into their dApp or smart contract. Other pool creators can use one of the community-managed front ends \(see: [Katana Pools](https://katanapools.com/)\) to customize and deploy their pools.
{% endhint %}

## Step 1: Getting the ConverterRegistry Address and ABI

The first step is getting the address of the `ConverterRegistry` contract. In order to get the current address of the `ConverterRegistry` you can query the `ContractRegistry`. The following snippet will provide Converter Registry address.

{% tabs %}
{% tab title="Web3.js" %}
```javascript
const Web3 = require("web3");

const NODE_ADDRESS = "https://mainnet.infura.io/v3/<INFURA_ID>";
const web3 = new Web3(NODE_ADDRESS);

const contractRegistry = new web3.eth.Contract(CONTRACT_REGISTRY_ABI, CONTRACT_REGISTRY_ADDRESS);

const getConverterRegistryAddress = async () => (
    await contractRegistry.methods.addressOf(Web3.utils.asciiToHex("BancorConverterRegistry")).call();
)

// if you're interested in saving the variable locally:
const converterRegistryAddress = getConverterRegistryAddress();

// if you want to log it to the console:
console.log(`Converter Registry address: ${converterRegistryAddress}`);
```
{% endtab %}

{% tab title="Ethers" %}
```javascript
const { ethers } = require("ethers");

const INFURA_PROJECT_ID = "<INFURA_ID>";
const provider = ethers.getDefaultProvider("mainnet", INFURA_PROJECT_ID);

const contractRegistry = new ethers.Contract(CONTRACT_REGISTRY_ADDRESS, CONTRACT_REGISTRY_ABI, provider);

const getConverterRegistryAddress = async () => (
    await contractRegistry.addressOf(ethers.utils.formatBytes32String("BancorConverterRegistry"));
)

// if you're interested in saving the variables locally:
const converterRegistryAddress = getConverterRegistryAddress();

// if you want to log it to the console:
console.log(`Converter Registry address: ${converterRegistryAddress}`);
```
{% endtab %}
{% endtabs %}

Once you have the Contract Registry addresses, get their ABIs from the documentation here, or alternatively from [Etherscan](https://etherscan.io) by clicking on the "Contract" tab \(underneath the contract source code\). You'll need the ABI to connect with these contracts further on.

## Step 2: Deploying a new Liquidity Pool

The **newConverter** function on the `ConverterRegistry` contract takes the following parameters:

* `_type`: converter type
* `_name`: name of your token or pool
* `_symbol`: symbol for your token or pool
* `_decimals`: number of decimals on your pool token
* `_maxConversionFee`: maximum trading fee in parts per million for transactions passing through your converter
* `_reserveTokens`: the addresses of the token reserves to be held in your pool
* `_reserveWeights`: the relative weights of each reserve token in parts per million. The sum of the values in this array must be 1,000,000.

We'll describe how to use this function from Web3 libraries, and also how to call it from a smart contract.

### Integration through Web3

If you're developing with Web3, you'll first need to copy the `ContractRegistry` and `ConverterRegistry` ABIs into your project. You can find them here in the docs or through [Etherscan](https://etherscan.io/) \(seacrh for `Bancor: Contract Registry`, then click on the "Contract" tab, and use the `addressOf` function to find the `ConverterRegistry`, then get the ABI for the `ConverterRegistry` there.\).

And your Web3 implementation would look something like this, we've coded in the current mainnet Contract Registry address for convenience:

{% tabs %}
{% tab title="Web3.js" %}
```javascript
const Web3 = require("web3");
const ContractRegistryABI = require('./ContractRegistry.json');
const ConverterRegistryABI = require('./ConverterRegistry.json');

const NODE_ADDRESS = "https://mainnet.infura.io/v3/<INFURA_ID>";
const web3 = new Web3(NODE_ADDRESS);

const ContractRegistryAddress = '0x52Ae12ABe5D8BD778BD5397F99cA900624CfADD4';
const ContractRegistryContract = new web3.eth.Contract(
    ContractRegistryABI,
    ContractRegistryAddress
)

const getConverterRegistryAddress = async() => {
    const converterRegistryName = web3.utils.asciiToHex('ConverterRegistry');
    const address = await ContractRegistryContract.methods.addressOf(converterRegistryName).call();
    return address;
} 

const deployConverter = async() => {
    const converterRegistryAddress = await getConverterRegistryAddress();
    const ConverterRegistryContract = new web3.eth.Contract(
        ConverterRegistryABI,
        converterRegistryAddress
    );

    const type = 1;
    const name = 'XYZABC Pool Token'
    const symbol = 'XYZABC';
    const decimals = 18;
    // 3% max fee
    const maxConversionFee = 30000; 
    const reserveTokens = [XYZ_TOKEN_ADDRESS, ABC_TOKEN_ADDRESS];
    // ratio of 3 XYZ to every 7 ABC
    const reserveWeights = [300000, 700000];

    await ConverterRegistryContract.methods.newConverter(
        type,
        name,
        symbol,
        decimals,
        maxConversionFee,
        reserveTokens,
        reserveWeights
    ).send();
}
```
{% endtab %}

{% tab title="Ethers" %}
```javascript
const { ethers } = require("ethers");
const ContractRegistryABI = require('./ContractRegistry.json');
const ConverterRegistryABI = require('./ConverterRegistry.json');

const INFURA_PROJECT_ID = "<INFURA_ID>";
const provider = ethers.getDefaultProvider("mainnet", INFURA_PROJECT_ID);

const contractRegistry = new ethers.Contract(
    '0x52Ae12ABe5D8BD778BD5397F99cA900624CfADD4', 
    ContractRegistryABI, 
    provider
);

const getConverterRegistryAddress = async() => {
    const converterRegistryName = ethers.utils.formatBytes32String('ConverterRegistry');
    const address = await ContractRegistryContract.addressOf(converterRegistryName);
    return address;
} 

const deployConverter = async() => {
    const converterRegistryAddress = await getConverterRegistryAddress();
    const ConverterRegistryContract = new ethers.Contract(
        converterRegistryAddress,
        ConverterRegistryABI
    );

    const type = 1;
    const name = 'XYZABC Pool Token'
    const symbol = 'XYZABC';
    const decimals = 18;
    // 3% max fee
    const maxConversionFee = 30000;
    const reserveTokens = [XYZ_TOKEN_ADDRESS, ABC_TOKEN_ADDRESS];
    // ratio of 3 XYZ to every 7 ABC
    const reserveWeights = [300000, 700000]; 

    await ConverterRegistryContract.methods.newConverter(
        type,
        name,
        symbol,
        decimals,
        maxConversionFee,
        reserveTokens,
        reserveWeights
    ).send();
}
```
{% endtab %}
{% endtabs %}

### Integration through Smart Contracts

For integration via smart contract, use the Solidity interfaces below. You'll first need to query for the current address of the `ConverterRegistry` contract before executing the **newConverter** function (see above), we've coded in the current mainnet address for convenience.

```solidity
interface IContractRegistry {
    function addressOf(
        bytes32 contractName
    ) external returns(address);
}

interface IConverterRegistry {
    function newConverter (
        uint16 _type,
        string _name,
        string _symbol,
        uint8 _decimals,
        uint32 _maxConversionFee,
        IERC20Token[] memory _reserveTokens,
        uint32[] memory _reserveWeights
    ) external returns (IConverter);
}
```
You'll also need an interface for ERC20 tokens. Any standard IERC20 (OpenZeppelin, Boring) will suffice. Alternatively you can use:
```solidity
interface IERC20 {
    function name() external view returns (string memory);
    function symbol() external view returns (string memory);
    function decimals() external view returns (uint8);
    function totalSupply() external view returns (uint256);
    function balanceOf(address _owner) external view returns (uint256);
    function allowance(address _owner, address _spender) external view returns (uint256);
    function transfer(address _to, uint256 _value) external returns (bool);
    function transferFrom(
        address _from,
        address _to,
        uint256 _value
    ) external returns (bool);
    function approve(address _spender, uint256 _value) external returns (bool);
}
```
The following describes a contract which can be used to deploy new liquidity pools by calling the `ConverterRegistry`:
```solidity
// you'll need the ContractRegistry and Converter Registry interfaces, import or put them in the same file
// you'll also need a ERC20 interface

contract MyContract {
    // mainnet - you can use the method in step 1 to retrieve the address
    address contractRegistry = 0x52Ae12ABe5D8BD778BD5397F99cA900624CfADD4;
    bytes32 converterRegistryName = 'BancorConverterRegistry';

    function deployNewConverter(
        uint16 _type,
        string _name,
        string _symbol,
        uint8 _decimals,
        uint32 _maxConversionFee,
        IERC20[] memory _reserveTokens,
        uint32[] memory _reserveWeights 
    ) external returns(address) {
        IContractRegistry contractRegistry = IContractRegistry(contractRegistry);
        address converterRegistry = contractRegistry.addressOf(converterRegistryName);
        IConverter converter = IConverterRegistry(converterRegistry).newConverter(
            _type,
            _name,
            _symbol,
            _decimals,
            _maxConversionFee,
            _reserveTokens,
            _reserveWeights
        );
        return address(converter);
    }
}
```
You'll still need to call the `deployNewConverter()` function in order to deploy a new liquidity pool. The code for doing so using a web3 library should be similar to the code above. Alternatively, you could use a platform like [Remix](https://remix.ethereum.org) or Etherscan's "Contract" tab (assuming the contract was verified).
## Step \#2 - Adding Oracle and Activating the pool

{% hint style="warning" %}
This step is only relevant for Bancor V2 pools (not V2.1, not pre-V2)
{% endhint %}

Following pool creation, Bancor V2 pools require setting the relevant price oracles. The pool will only be activated once this function is called.

```solidity
function activate(
    address _primaryReserveToken, 
    address _primaryReserveOracle, 
    address _secondaryReserveOracle
);
```

|  |  |
| :--- | :--- |
| `_primaryReserveToken` | `address of the pool's primary reserve token` |
| `_primaryReserveOracle` | `address of a Chainlink price oracle for the primary reserve token` |
| `_secondaryReserveOracle` | `address of a Chainlink price oracle for the secondary reserve token` |

{% hint style="info" %}
Make sure you use trusted oracles that are part of the white list
{% endhint %}

{% hint style="info" %}
Oracles must use the same pair. It is recommended to use oracles with price to ETH
{% endhint %}

## Step \#3 - Accepting Ownership

As the capstone to the pool creation process, make sure to execute the `acceptOwnership` function on the new converter contract. Etherscan should have automatically verified your contract and this should be easily callable from their UI, or alternatively via your Web3 connection or smart contract interface.

