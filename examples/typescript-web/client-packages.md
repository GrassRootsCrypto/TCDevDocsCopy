---
description: Using xchain client packages
---

# Client Packages

### F**ull Multichain Wallet Example**

A wallet class has been created that instantiates every chain client and leverages the interface which greatly simplifies working with wallets and THORChain. See the below code example.

{% embed url="https://replit.com/@thorchain/xchain-wallet#index.ts" %}

### Client Packages breakdown

Client packages have been created for each blockchain that connects to THORChain. All clients implement xchain-crypto which acts like a super class and gives each client a common interface.&#x20;

Common functions with code examples are:

### **Config and Set Up of a Wallet**

{% embed url="https://replit.com/@thorchain/TestBed#index.ts" %}
Some chains require address history to query balances and Txs
{% endembed %}

### **Querying**

All clients implement these functions. While most can use the same code, some have slight client differences.&#x20;

* Get Explorer URL - for the specific blockchain
* Get Balance - returns the balance of an address
* Get Transactions - returns a simplified array of recent transactions for an address.
* Get Transaction Data - returns transaction information from the transaction ID/hash.

See below for a Bitcoin example. Also see [Ethereum](https://replit.com/@thorchain/xchain-ethereum#index.ts), [Binance](https://replit.com/@thorchain/xchain-binance#index.ts), [THORChain](https://replit.com/@thorchain/xchain-thorchain), [Cosmos](https://replit.com/@thorchain/xchain-cosmos#index.ts), and [Avalance ](https://replit.com/@thorchain/xchain-avax)examples. &#x20;

{% embed url="https://replit.com/@thorchain/xchain-bitcoin#index.ts" %}
Some chains require address history to query balances and Txs
{% endembed %}

### Transactions

* Get Fees - get the transaction fee for the chain, separate from THORChain fees
* Transfer - transfer funds from one wallet to another.&#x20;
* Purge - When a wallet is "locked" the private key should be purged in each client by setting it back to null.

See [http://docs.xchainjs.org/xchain-client/overview.html](http://docs.xchainjs.org/xchain-client/overview.html) for more information.&#x20;





