# Harbinger

## Introduction

Harbinger is an oracle system for the [Tezos Blockchain]() which provides a price oracle for assets. 

Harbinger works according to the principles of [Compound's Open Oracle System](). Separate entities provide signed price updates and pay fees for putting the data on chain. This separation of responsibility leads to a decentralized solution that benefits the entire ecosystem. 

Harbinger stores "candles" of prices of assets. Specifically, each asset in a Harbinger Oracle contains the following data: (1) start time, (2) end time, (3) open price, (4) high price, (5) low price, (6) close price, and (7) volume. 

## Quickstart

The easiest way to get started with Harbinger is to install and use the [Harbiner CLI]():

```shell
$ npm i -g harbinger
$ harbinger --help
```

Contracts for Harbinger are deployed at the following addresses:
```
# Mainnet Oracle Contract
[KT1]() # TODO(keefertaylor): Deploy and update this address.

# Mainnet Normalizer Contract
[KT1]() # TODO(keefertaylor): Deploy and update this address.

# CarthageNet Oracle Contract
[KT1]() # TODO(keefertaylor): Deploy and update this address.

# CarthageNet Oracle Contract
[KT1]() # TODO(keefertaylor): Deploy and update this address.
```

## Repositories

Harbinger is made up of several components in separate github repositories for discoverability. Developers, signers, posters and users may be interested in one or more of the following repositories:

- [harbinger-contracts](): Contains reference implementations for Harbinger's smart contracts.
- [harbinger-cli](): Contains a CLI interface to deploy, update and interact with Harbiner's smart contracts.
- [harbinger-poster](): Contains a reference implementation of a posting service via AWS.
- [harbinger-signer](): Contains a reference implementation of a signing service via AWS.
- [harbinger-lib](): Contains a core library shared among the above components. 

## Details

### Components

Harbinger divorces the price feed from payment of fees to place the price feed on chain. 

At a high level, three components make up Harbinger:
- **Oracle Contract**: A smart contract which keeps track of data on chain.
- **Signers**: A service which provides and signs price data. 
- **Posters**: A client which moves data from the **signers** to the **oracle contract** and pays associated fees.

Oracle contracts are initialized with the public key of a signer. As long as updates are signed correctly, they can be posted to an Oracle. 

### Signers

Any service can sign updates for the Harbinger Oracle system as long as they sign the correct data. The following services provide price feeds:
- [Coinbase Pro]()

### Contracts

Harbiner is made up of two contracts:
- **Oracle Contract**: Stores a mapping of assets to candle data at a specific point in time. 
- **Normalizer Contract**: Normalizes *n* data points from an Oracle by computing a volume weighted average price for the updates.

Data is posted to the Oracle contract and then can be pushed to the Normalizer contract by the same poster or another entity. 

### Providers

## Technical Specifications

### Oracle Data

All oracle data is represented in two formats:
- A standard unix timestamp for candle starts and ends
- A natural number for prices and volumes, with six digits of precision. For instance, the price `$123.45` would be represented as `123450000`.

### Asset Codes

The following asset codes are a convention in Harbinger. 

### Oracle Contract Specifications

Oracle contracts are bound to a single price feed and can contain an unlimited number of assets. All updates must be monotonically increasing in start time.

Specifically, the oracle contract can be configured with the following parameters:
- **Signer Public Key:** The public key of the **signer** which provides the price feed for the oracle.
- **Asset List:** A list of assets that the oracle will keep track of. Higher numbers of assets lead to increased gas and storage fees when interacting with the oracle. 

An oracle contract has the following entrypoints:
- **`update`**: Receives an signed set of updates to the oracle contract.
- **`push`**: Pushes the data in the Oracle to a normalizer contract.
- **`revoke`**: Revokes an oracle contract by removing the signer public key. This effectively triggers an emergency shutdown of the Oracle.

### Normalizer Contract Specifications

Normalizer contracts normalize a single asset and can only receive updates from one Oracle.  All updates must be monotonically increasing in start time.

Specifically, the normalizer contract can be configured with the following parameters:
- **Oracle Contract Address:** The address of the oracle contract which provides updates.
- **Asset Name:** The name of the asset that the contract will normalize.
- **Number of Data Points:**: The number of data points to store and normalize. Higher numbers of data points lead to increased gas and storage fees when interactin with the normalizer.

A normalizer contract has the following entrypoints:
- **`update`**: Receives data from an oracle contract.

## Credits 

Harbinger is written and maintained by [Luke Youngblood]() and [Keefer Taylor](). 
