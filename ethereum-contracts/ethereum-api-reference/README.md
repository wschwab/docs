# API Reference

\# Bancor Protocol Contracts v0.6 \(beta\)

[![Docs](https://img.shields.io/badge/docs-%F0%9F%93%84-blue)](https://docs.bancor.network/) [![NPM Package](https://img.shields.io/npm/v/@bancor/contracts-solidity.svg)](https://www.npmjs.org/package/@bancor/contracts-solidity)

## Overview

The solidity version of the Bancor smart contracts is composed of many different components that work together to create the Bancor Network deployment.

The main contracts are the BancorNetwork contract \(entry point to the system\) and the different converter contracts \(implementation of liquidity pools and their reserves\).

## Upgradeability

All smart contract functions are public and all upgrades are opt-in. If significant improvements are made to the system a new version will be released. Token owners can choose between moving to the new system or staying in the old one. If possible, new versions will be backwards compatible and able to interact with the old versions.

## Language

The terms “reserves” and “connectors” have the same meaning throughout Bancor’s smart contract code and documentation. “Reserve ratio” and “connector weight” are also used interchangeably. “Connector balance” refers to the token inventories held in a liquidity pool's reserves.

## Warning

Bancor is a work in progress. Make sure you understand the risks before using it.

## Testing

### Prerequisites

* node 10.16.0
* yarn 1.22.0
* python 3.7.3
* web3.py 4.9.2

### Installation

* `yarn`

### Verification

* Verifying all the contracts:
  * `yarn test` \(quick testing\)
  * `yarn coverage` \(full coverage\)
* [Verifying the BancorFormula contract](https://github.com/bancorprotocol/docs/tree/9fb588eb5555a0d026c116a8c261fd0dea91b170/ethereum-contracts/ethereum-api-reference/solidity/python/README.md)

### [Utilities](https://github.com/bancorprotocol/docs/tree/9fb588eb5555a0d026c116a8c261fd0dea91b170/ethereum-contracts/ethereum-api-reference/solidity/utils/README.md)

## Collaborators

* [**Yudi Levi**](https://github.com/yudilevi)
* [**Barak Manos**](https://github.com/barakman)
* [**Leonid Beder**](https://github.com/lbeder)
* [**Ilana Pinhas**](https://github.com/ilanapi)
* [**David Benchimol**](https://github.com/davidbancor)
* [**Or Dadosh**](https://github.com/ordd)
* [**Martin Holst Swende**](https://github.com/holiman)

## License

Bancor Protocol is open source and distributed under the Apache License v2.0

