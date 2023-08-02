---
description: XChainJS library overview and the main components
---

# Overview

XChainJS is an open-source library with a common interface for multiple blockchains, built for simple and fast integration for wallets and Dexs and more. xChainjs is designed to abstract THORChain and specific blockchain complexity and to provide an easy-to-use API for developers.&#x20;

The packages implement the complexity detailed in the other sections of this site.&#x20;

xChain has several key modules allowing powerful functionality.&#x20;

### **Thorchain-query**&#x20;

Allows easy information retrieval and estimates from THORChain.

{% content-ref url="query-package.md" %}
[query-package.md](query-package.md)
{% endcontent-ref %}

### **Thorchain-amm**

Conducts actions such as swap, add and remove. It wraps xchain clients and creates a new wallet class for and balance collection.

{% content-ref url="amm-package.md" %}
[amm-package.md](amm-package.md)
{% endcontent-ref %}

### **Chain clients**

For every blockchain connected to THORChain with a common interface.&#x20;

Current clients implemented are**:**

* xchain-avax
* xchain-binance&#x20;
* xchain-bitcoin&#x20;
* xchain-bitcoincash&#x20;
* xchain-cosmos&#x20;
* xchain-doge
* xchain-ethereum
* xchain-litecoin
* xchain-mayachain
* xchain-thorchain

{% content-ref url="client-packages.md" %}
[client-packages.md](client-packages.md)
{% endcontent-ref %}

**APIs** for getting data from THORChain.

* Midgard
* Thornode

{% content-ref url="packages-breakdown.md" %}
[packages-breakdown.md](packages-breakdown.md)
{% endcontent-ref %}

See the package breakdown for more information.&#x20;

### Install Procedures

Ensure you have the following

* npm --version v8.5.5 or above
* node --version v16.15.0
* yarn --version v1.22.18 or above &#x20;

Create a new project by creating a new folder, then type `npx tsc --init`.

#### Finding required dependencies

The replit code examples have all the required packages within the project.json file, just copy the project dependencies into your own project.json.&#x20;

Example for the [query-package](query-package.md), [estimateSwap](query-package.md#estimate-swap) packages

1. Go to the replit code example then press show files. Select the project.json file.
2. Locate and then copy the `dependencies` section into your project.json file.&#x20;
3. From the command line, type `yarn`. This will download and install the required packages. &#x20;

The code is available on [GitHub](https://github.com/xchainjs/xchainjs-lib/) and managed by several key developers. Reach out at Telegram group: [https://t.me/xchainjs](https://t.me/xchainjs) for more information.&#x20;
