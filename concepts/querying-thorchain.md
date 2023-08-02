---
description: How to Query THORChain
---

# Querying THORChain

### Getting the Asgard Vault

Vaults are fetched from the `/inbound_addresses` :

[https://thornode.ninerealms.com/thorchain/inbound\_addresses](https://thornode.ninerealms.com/thorchain/inbound\_addresses)

You need to select the address of the Chain the inbound transaction will go to.&#x20;

The address will be the current active Asgard Address that accepts inbounds. Do not cache these address as they change regularly. Do not delay inbound transactions (e.g. do not use future timeLocks).

Example Output, each connected chain will be displayed.&#x20;

```json
[
  {
      "address": "bc1q2taly7tynxvmmw5n2048wv56cyhmnc6lvx7737",
      "chain": "BTC",
      "chain_lp_actions_paused": false,
      "chain_trading_paused": false,
      "dust_threshold": "10000",
      "gas_rate": "19",
      "gas_rate_units": "satsperbyte",
      "global_trading_paused": false,
      "halted": false,
      "outbound_fee": "39000",
      "outbound_tx_size": "1000",
      "pub_key": "thorpub1addwnpepq22rph4ed3nkp6lp060nmuqy3p0axadaklnvcs4qfrgyq6zl0rrux9jxkxj"
    },
      ...
]
```

{% hint style="danger" %}
Never cache vault addresses, they churn regularly.&#x20;
{% endhint %}

{% hint style="danger" %}
Inbound transactions should not be delayed for any reason else there is risk funds will be sent to an unreachable address. Use standard transactions, check the `inbound address` before sending and use the recommended [`gas rate`](querying-thorchain.md#getting-the-asgard-vault) to ensure transactions are confirmed in the next block to the latest `Inbound_Address`.
{% endhint %}

{% hint style="danger" %}
Check for the `halted` parameter and never send funds if it is set to true
{% endhint %}

{% hint style="warning" %}
If a chain has a `router` on the inbound address endpoint, then everything must be deposited via the router. The router is a contract that the user first approves, and the deposit call transfers the asset into the network and emits an event to THORChain.&#x20;

\
This is done because "tokens" on protocols don't support memos on-chain, thus need to be wrapped by a router which can force a memo.&#x20;

Note: you can transfer the base asset, eg ETH, directly to the address and skip the router, but it is recommended to deposit everything via the router.&#x20;

```
{  "address": "0x500b62a37c1afe79d59b373639512d03e3c4f5e8",
   "chain": "ETH",
   "gas_rate": "70",
   "halted": false,
   "pub_key": "thorpub1addwnpepq05w4xwaswph29ksls25ymjkypav30t8ktyu2dqzkxqk3pkf2l5zklvfzef",
   "router": "0xD37BbE5744D730a1d98d8DC97c42F0Ca46aD7146"
   }
```
{% endhint %}

{% hint style="warning" %}
If you connect to a public Midgard, you must be conscious of the fact that you can be phished and could send money to the WRONG vault. You should do safety checks, i.e. comparing with other nodes, or even inspecting the vault itself for the presence of funds. You should also consider running your own '[fullnode](https://docs.thorchain.org/thornodes/overview)' instance to query for trusted data.
{% endhint %}

* `Chain`: Chain Name
* `Address`: Asgard Vault inbound address for that chain.,&#x20;
* `Halted`: Boolean, if the chain is halted. This should be monitored.
* `gas_rate`: rate to be used, e.g. in Stats or GWei. See Fees.

### Displaying available pairs

Use the `/pools` [endpoint ](https://midgard.thorchain.info/v2/pools)of Midgard to retrieve all swappable assets on THORChain. The response will be an array of objects like this:

```json
{
    "asset": "BNB.BTCB-1DE",
    "assetDepth": "11262499812",
    "assetPrice": "11205.698400479405",
    "assetPriceUSD": "38316.644098172634",
    "liquidityUnits": "61816750778660",
    "poolAPY": "0.24483254735655713",
    "runeDepth": "126204176128728",
    "status": "available",
    "synthSupply": "0",
    "synthUnits": "0",
    "units": "61816750778660",
    "volume24h": "67544420820530"
}
```

{% hint style="info" %}
Only pools with `"status": "available"` are available to trade
{% endhint %}

{% hint style="info" %}
Make sure to manually add Native $RUNE as a swappable asset.
{% endhint %}

{% hint style="info" %}
`"assetPrice" tells you the asset's price in RUNE (RUNE Depth/AssetDepth ). In the above example`

`1 BNB.BTCB-1DE = 11,205 RUNE`
{% endhint %}

### Decimals and Base Units

All values on THORChain (thornode and Midgard) are given in 1e8 eg, 100000000 base units (like Bitcoin), and unless postpended by "USD", they are in units of RUNE. Even 1e18 assets, such as ETH.ETH, are shortened to 1e8. 1e6 Assets like ETH.USDC, are padded to 1e8. THORNode will tell you the decimals for each asset, giving you the opportunity to convert back to native units in your interface.&#x20;

See code examples using the THORChain xchain package here [https://github.com/xchainjs/xchainjs-lib/tree/master/packages/xchain-thorchain](https://github.com/xchainjs/xchainjs-lib/tree/master/packages/xchain-thorchain)&#x20;

### Finding Chain Status

There are two ways to see if a Chain is halted.&#x20;

1. Looking at the `/inbound_addresses` [endpoint](https://thornode.ninerealms.com/thorchain/inbound\_addresses) and inspecting the halted flag.
2. Looking at Mimir and inspecting the HALT\[Chain]TRADING setting. See [network-halts.md](network-halts.md "mention") for more details.
