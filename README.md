---
description: Overview of THORChain and links to frontend guides.
---

# Introduction

## Overview

THORChain is a decentralised cross-chain liquidity protocol that allows users to add liquidity or swap over that liquidity. It does not peg or wrap assets. Swaps are processed as easy as making a single on-chain transaction.&#x20;

THORChain works by observing transactions to its vaults across all the chains it supports. When the majority of nodes observe funds flowing into the system, they agree on the user's intent (usually expressed through a [memo](concepts/memos.md) within a transaction) and take the appropriate action.

{% hint style="info" %}
For more information see [Understanding THORChain, ](https://docs.thorchain.org/learn/understanding-thorchain)[Technology](https://docs.thorchain.org/how-it-works/technology) or [Concepts](broken-reference).
{% endhint %}

For wallets/interfaces to interact with THORChain, they need to:

1. Connect to THORChain to obtain information from one or more endpoints.
2. Construct transactions with the correct memos.
3. Send the transactions to THORChain Inbound Vaults.

{% hint style="info" %}
[Front-end](./#front-end-development-guides) guides have been developed for fast and simple implementation.
{% endhint %}

### Connecting to THORChain

THORChain has several APIs with Swagger documentation, see here for a full list.&#x20;

* Midgard - [https://midgard.ninerealms.com/v2/doc](https://midgard.ninerealms.com/v2/doc)
* THORNode - [https://thornode.ninerealms.com/thorchain/doc](https://thornode.ninerealms.com/thorchain/doc)
* Cosmos RPC - [https://v1.cosmos.network/rpc/v0.45.1](https://v1.cosmos.network/rpc/v0.45.1), [Example Link](https://stagenet-thornode.ninerealms.com/cosmos/base/tendermint/v1beta1/blocks/latest)

See [connecting-to-thorchain.md](concepts/connecting-to-thorchain.md "mention")[ ](concepts/connecting-to-thorchain.md)for more information.

### Quote Interfaces

Several endpoints have been created within Thornode allowing easy implementation for front-end developers. See the Quote section within [Thornode documentation](https://thornode.ninerealms.com/thorchain/doc) and examples within the[#front-end-development-guides](./#front-end-development-guides "mention").

### Support and Questions

Join the [THORChain Dev Discord ](https://discord.gg/7RRmc35UEG)for any questions or assistance.&#x20;

## Front-end Development Guides

### Native Swaps Guide

Frontend developers can use THORChain to access decentralised layer1 swaps between BTC, ETH, BNB, ATOM and more.

{% content-ref url="swap-guide/quickstart-guide.md" %}
[quickstart-guide.md](swap-guide/quickstart-guide.md)
{% endcontent-ref %}

### Native Savings Guide

THORChain offers a Savings product, which earns yield from Swap fees. Deposit Layer1 Assets to earn in-kind yield. No lockups, penalties, impermanent loss, minimums, maximums or KYC.

{% content-ref url="saving-guide/quickstart-guide.md" %}
[quickstart-guide.md](saving-guide/quickstart-guide.md)
{% endcontent-ref %}

### Aggregators

Aggregators can deploy contracts that use custom `swapIn` and `swapOut` cross-chain aggregation to perform swaps before and after THORChain.&#x20;

Eg, swap from an asset on Sushiswap, then THORChain, then an asset on TraderJoe in one transaction.&#x20;

{% content-ref url="aggregators/aggregator-overview.md" %}
[aggregator-overview.md](aggregators/aggregator-overview.md)
{% endcontent-ref %}

### Concepts

In-depth guides to understand THORChain's implementation have been created.

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

### Libraries

Several libraries exist to allow for rapid integration. xchainjs has seen the most development is recommended.&#x20;

{% content-ref url="concepts/code-libraries.md" %}
[code-libraries.md](concepts/code-libraries.md)
{% endcontent-ref %}

Eg, swap from layer 1 ETH to BTC and back.&#x20;

{% embed url="https://docs.xchainjs.org/overview/" %}

### Analytics

Analysts can build on Midgard or Flipside to access cross-chain metrics and analytics.&#x20;

Eg, gather information on cross-chain liquidity

{% content-ref url="concepts/connecting-to-thorchain.md" %}
[connecting-to-thorchain.md](concepts/connecting-to-thorchain.md)
{% endcontent-ref %}

