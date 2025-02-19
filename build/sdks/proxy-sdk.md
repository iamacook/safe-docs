# Proxy SDK

Contract accounts on Ethereum in many respects offer more powerful functionality than do ordinary EOAs \(externally owned accounts\):

* Contract accounts can make multiple calls to different contracts in a single transaction;
* Token standards like ERC721 and ERC1155 allow contracts to immediately react to receiving tokens through a callback;
* Services can interact on behalf of a contract in specific capacities.

The Contract Proxy Kit \(CPK\) seeks to bring the power of contract interactions to users by assigning users a predetermined proxy account that can act as a proxy on a user’s behalf. The proxy itself is a single-owner Gnosis Safe instance, which enables batch interactions, fallback handler registration, and custom module installation.

Currently, only batch transactions are directly supported by the CPK. This capability simplifies user flows for dapps that require multiple, sequential interactions with different contracts to be done in what should be a single transaction. For a prominent example, consider most dapps using the ERC20 standard. Often, dapps have to ask the user to approve an amount of ERC20 to be used before asking a dapp contract to pull the amount from the user’s account. This is a common pair of transactions that should be a single action, but would by default trigger two transactions when batch transactions are not available. Some more complex examples may involve coordinating interactions with multiple contract systems.

#### Adding the dependencies

The contract proxy kit is only available as a js library via npm and can be added to your project with

`npm install contract-proxy-kit`

or

`yarn add contract-proxy-kit`

### Initializing the library

The contract proxy kit can be used with [ethers.js](https://github.com/ethers-io/ethers.js/) or with [web3.js](https://github.com/ethereum/web3.js/). To initialize the contract proxy kit you need to provide these providers to the library.

First we import the library

```text
const CPK = require('contract-proxy-kit')
```

Then we create an instance of the contract proxy kit providing the providerweb3.jsethers.js

```text
import CPK, { Web3Adapter } from 'contract-proxy-kit';
import Web3 from 'web3';

const web3 = new Web3(/*...*/);

const ethLibAdapter = new Web3Adapter({ web3 });

const cpk = await CPK.create({ ethLibAdapter });
```

This will use the address of the provider to check for an existing proxy. If none exists it will create a new proxy when the user first performs a transaction.

### Usage of the CPK instance

Now that the instance is setup it is possible to check the address of the proxy.

Note: This address is available even before the proxy is deployed due to [counterfactual deployment](https://docs.gnosis.io/safe/docs/contracts_deployment).

```text
cpk.address
```

It is also possible to check the owner of this proxy.

```text
const ownerAccount = await cpk.getOwnerAccount()
```

To perform transactions with the contract proxy kit we need to pass an array of transactions. These transactions will be batched into one transaction that is submitted to the proxy.

```text
await cpk.execTransactions([
  {
    operation: CPK.Call, // Not needed because this is the default value.
    data: '0x', // Not needed because this is the default value.
    to: '0x90f8bf6a479f320ead074411a4b0e7944ea8c9c1',
    value: `${1e18}`,
  },
  {
    // Without default values
    to: '0xffcf8fdee72ac11b5c542428b35eef5769c409f0',
    value: `${1e18}`,
  },
]);
```

### Advanced features

For extensive documentation check our [GitHub repository](https://github.com/gnosis/contract-proxy-kit) and the [CPK tutorials](https://docs.gnosis.io/safe/docs/sdks_tutorials)

