---
description: Interfaces need to monitor and react to keep network paramaters.
---

# Network Halts

{% hint style="warning" %}
If the network is halted, do not send funds. The easiest check to do is if `halted = true` on the inbound addresses endpoint.&#x20;
{% endhint %}

{% hint style="info" %}
In most cases funds won't be lost if they are sent when halted, but they may be significantly delayed.&#x20;
{% endhint %}

{% hint style="danger" %}
In the worse case if THORChain suffers a consensus halt the `inbound_addresses` endpoint will freeze with `halted = false` but the network is actually hard-halted. In this case running a fullnode is beneficial, because the last block will become stale after 6 seconds and interfaces can detect this.&#x20;
{% endhint %}

Interfaces that provide LP management can provide more feedback to the user what specifically is halted.&#x20;

There are levels of granularity the network has to control itself and chains in the event of issues. Interfaces need to monitor these settings and apply appropriate controls in their interfaces, inform users and prevent unsupported actions.&#x20;

All activity is controlled within [Mimir](https://midgard.thorchain.info/v2/thorchain/mimir) and needs to be observed by interfaces and acted upon. Also, see a description of [Constants and Mimir](https://docs.thorchain.org/network/constants-and-mimir).&#x20;

Halt flags are Boolean. For clarity `0` = false, no issues and `> 0` = true (usually 1), halt in effect.&#x20;

### Halt/ Pause Management

Each chain has granular control allowing each chain to be halted or resumed on a specific chain as required. Network-level halting is also possible.

1. **Specific Chain Signing Halt** - Allows inbound transactions but stops the signing of outbound transactions. Outbound transactions are [queued](https://thornode.ninerealms.com/thorchain/queue). This is the least impactful halt.&#x20;
   1. Mimir setting is `HALTSIGNING[Chain]`, e.g. `HALTSIGNINGBNB`
2. **Specific Chain Liquidity Provider Pause -** addition and withdrawal of liquidity are suspended but swaps and other transactions are processed.&#x20;
   1. Mimir setting is `PAUSELP[Chain]`, e,g, `PAUSELPBCH`for BCH
3. **Specific Chain Trading Halt** - Transactions on external chains are observed but not processed, only [refunds ](memos.md#refunds)are given.  THORNode's Bifrost is running, nodes are synced to the tip therefore trading resumption can happen very quickly.&#x20;
   1. Mimir setting is `HALT[Chain]TRADING`, e,g, `HALTBCHTRADING`for BCH
4. **Specific Chain Halt** - Serious halt where transitions on that chain are no longer observed and THORNodes will not be synced to the chain tip, usually their Bifrost offline. Resumption will require a majority of nodes syncing to the tip before trading can commence.&#x20;
   1. Mimir setting is `HALT[Chain]CHAIN`, e,g, `HALTBCHCHAIN` for BCH.

{% hint style="warning" %}
Chain specific halts do occur and need to be monitored and reacted to when they occur. Users should not be able to send transactions via an interface when a halt is in effect.&#x20;
{% endhint %}

### **Network Level Halts**

**Network Pause LP**  `PAUSELP` = 1 Addition and withdrawal of liquidity are suspended for all pools but swaps and other transactions are processed.

**Network Trading Halt** `HALTTRADING = 1` will stop all trading for every connected chain. The THORChain blockchain will continue and native RUNE transactions will be processed.

There is no Network level chain halt setting as the THORChain Blockchain continually needs to produce blocks.

A chain halt is possible in which case Mimir or Midgard will not return data. This can happen if the chain suffers consensus failure or more than 1/3 of nodes are switched off. If this occurs the Dev Discord Server `#interface-alerts` will issue alerts.&#x20;

{% hint style="warning" %}
While very rare, a network level halt is possible and should be monitored for.&#x20;
{% endhint %}

### Synth Management

Synths minting and redeeming can be enabled and disabled using flags. There is also a Synth mint limit. The setting are:

* `MINTSYNTHS` - controls whether synths can be minted (swapping from L1 to synth)&#x20;
* `BURNSYNTHS` controls whether synths can be burned (swapping from synth to L1)
* `MAXSYNTHPERPOOLDEPTH` - controls the synth depth limit for each pool, expressed in basis points of the total pool depth (asset + RUNE). For example: `5000` basis points equals 50% of the total pool. If the pool contains 100 BTC and 100 BTC worth of RUNE, a 50% `MAXSYNTHPERPOOLDEPTH` allows 100 BTC of synthetic assets to be minted.

### ILP Management

* ILP is managed by the integer setting `FULLIMPLOSSPROTECTIONBLOCKS`, the number of blocks after which impermanent loss protection reaches 100% (zero = disabled). Impermanent loss protection scales linearly between an LP's `last_add_height` and `last_add_heigh`t + \```FULLIMPLOSSPROTECTIONBLOCKS`.``
* As of January 2023, nodes voted to `DEPRECATEILP`([ADR 005](https://gitlab.com/thorchain/thornode/-/blob/develop/docs/architecture/adr-005-deprecate-ilp.md)). As a result, the `ILPCUTOFF` mimir was set to block height `9450000` (approx. 30 days after node voted passed). After this block, new LPs (or changes to existing LPs) do not receive impermanent loss protection. Existing LPs that have not added to their LP continue to receive ILP in perpetuity.

See also [Constants and Mimir](https://docs.thorchain.org/network/constants-and-mimir).&#x20;
