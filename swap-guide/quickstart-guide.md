---
description: Make a cross-chain swap on THORChain in less than 5 minutes.
---

# Quickstart Guide

### Introduction

THORChain allows native L1 Swaps. On-chain [Memos](../concepts/memos.md) are used instruct THORChain how to swap, with the option to add [price limits](quickstart-guide.md#price-limits) and [affiliate fees](quickstart-guide.md#affiliate-fees). THORChain nodes observe the inbound transactions and when the majority have observed the transactions, the transaction is processed by threshold-signature transactions from THORChain vaults.

Let's demonstrate decentralized, non-custodial cross-chain swaps. In this example, we will build a transaction that instructs THORChain to swap native Bitcoin to native Ethereum in one transaction.

{% hint style="info" %}
The following examples use a free, hosted API provided by [Nine Realms](https://twitter.com/ninerealms\_cap). If you want to run your own full node, please see [connecting-to-thorchain.md](../concepts/connecting-to-thorchain.md "mention").
{% endhint %}

### 1. Determine the correct asset name.

THORChain uses a specific [asset notation](../concepts/memos.md#asset-notation). Available assets are at: [Pools Endpoint.](https://thornode.ninerealms.com/thorchain/pools)

BTC => `BTC.BTC`\
ETH => `ETH.ETH`&#x20;

{% hint style="info" %}
Only available pools can be used. (`where 'status' == Available)`
{% endhint %}

### 2. Query for a swap quote.

{% hint style="info" %}
All amounts are 1e8. Multiply native asset amounts by 100000000 when dealing with amounts in THORChain. 1 BTC = 100,000,000.
{% endhint %}

**Request**: _Swap 1 BTC to ETH and send the ETH to_ `0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430`.

[https://thornode.ninerealms.com/thorchain/quote/swap?from\_asset=BTC.BTC\&to\_asset=ETH.ETH\&amount=100000000\&destination=0x86d526d6624AbC0178cF7296cD538Ecc080A95F1](https://thornode.ninerealms.com/thorchain/quote/swap?from\_asset=BTC.BTC\&to\_asset=ETH.ETH\&amount=100000000\&destination=0x86d526d6624AbC0178cF7296cD538Ecc080A95F1)

**Response**:&#x20;

```json
{
  "dust_threshold": "10000",
  "expected_amount_out": "1619355520",
  "expiry": 1689143119,
  "fees": {
    "affiliate": "0",
    "asset": "ETH.ETH",
    "outbound": "240000"
  },
  "inbound_address": "bc1qpzs9rm82m08u48842ka59hyxu36wsgzqlt6e3t",
  "inbound_confirmation_blocks": 1,
  "inbound_confirmation_seconds": 600,
  "max_streaming_quantity": 0,
  "memo": "=:ETH.ETH:0x86d526d6624AbC0178cF7296cD538Ecc080A95F1",
  "notes": "First output should be to inbound_address, second output should be change back to self, third output should be OP_RETURN, limited to 80 bytes. Do not send below the dust threshold. Do not use exotic spend scripts, locks or address formats (P2WSH with Bech32 address format preferred).",
  "outbound_delay_blocks": 305,
  "outbound_delay_seconds": 1830,
  "recommended_min_amount_in": "60000",
  "slippage_bps": 49,
  "streaming_swap_blocks": 0,
  "total_swap_seconds": 2430,
  "warning": "Do not cache this response. Do not send funds after the expiry."
}
```

_If you send 1 BTC to `bc1qlccxv985m20qvd8g5yp6g9lc0wlc70v6zlalz8` with the memo `=:ETH.ETH:0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430`, you can expect to receive `13.4493552` ETH._&#x20;

_For security reasons, your inbound transaction will be delayed by 600 seconds (1 BTC Block) and 2040 seconds (or 136 native THORChain blocks) for the outbound transaction,_ 2640 seconds all up_. You will pay an outbound gas fee of 0.0048 ETH and will incur 41 basis points (0.41%) of slippage._

{% hint style="info" %}
Full quote swap endpoint specification can be found here: [https://thornode.ninerealms.com/thorchain/doc/. ](https://thornode.ninerealms.com/thorchain/doc/)

See an example implementation [here.](https://replit.com/@thorchain/quoteSwap#index.js)
{% endhint %}

If you'd prefer to calculate the swap yourself, see the [Fees](fees-and-wait-times.md) section to understand what fees need to be accounted for in the output amount. Also, review the [Transaction Memos](../concepts/memos.md) section to understand how to create the swap memos.&#x20;

### 3. Sign and send transactions on the from\_asset chain.

Construct, sign and broadcast a transaction on the BTC network with the following parameters:

Amount => `1.0`

Recipient => `bc1qlccxv985m20qvd8g5yp6g9lc0wlc70v6zlalz8`

Memo => `=:ETH.ETH:0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430`

{% hint style="warning" %}
Never cache inbound addresses! Quotes should only be considered valid for 10 minutes. Sending funds to an old inbound address will result in loss of funds.
{% endhint %}

{% hint style="info" %}
Learn more about how to construct inbound transactions for each chain type here: [Sending Transactions](../concepts/sending-transactions.md)
{% endhint %}

### 4. Receive tokens.

Once a majority of nodes have observed your inbound BTC transaction, they will sign the Ethereum funds out of the network and send them to the address specified in your transaction. You have just completed a non-custodial, cross-chain swap by simply sending a native L1 transaction.

## Additional Considerations

{% hint style="warning" %}
There is a rate limit of 1 request per second per IP address on /quote endpoints. It is advised to put a timeout on frontend components input fields, so that a request for quote only fires at most once per second. If not implemented correctly, you will receive 503 errors.
{% endhint %}

{% hint style="success" %}
For best results, request a new quote right before the user submits a transaction. This will tell you whether the _expected\_amount\_out_ has changed or if the _inbound\_address_ has changed. Ensuring that the _expected\_amount\_out_ is still valid will lead to better user experience and less frequent failed transactions.
{% endhint %}

### Price Limits

Specify _tolerance\_bps_ to give users control over the maximum slip they are willing to experience before canceling the trade. If not specified, users will pay an unbounded amount of slip.

[https://thornode.ninerealms.com/thorchain/quote/swap?amount=100000000\&from\_asset=BTC.BTC\&to\_asset=ETH.ETH\&destination=0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430\&tolerance\_bps=100](https://thornode.ninerealms.com/thorchain/quote/swap?amount=100000000\&from\_asset=BTC.BTC\&to\_asset=ETH.ETH\&destination=0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430\&tolerance\_bps=100)

```json
https://thornode.ninerealms.com/thorchain/quote/swap?amount=100000000&from_asset=BTC.BTC&to_asset=ETH.ETH&destination=0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430&tolerance_bps=100
```

Notice how a minimum amount (1342846539 / \~13.42 ETH) has been appended to the end of the memo. This tells THORChain to revert the transaction if the transacted amount is more than 100 basis points less than what the _expected\_amount\_out_ returns.

### Affiliate Fees

Specify affiliate\__address_ and _affiliate\_bps t_o skim a percentage of the _expected_\__amount\_out._

[https://thornode.ninerealms.com/thorchain/quote/swap?amount=100000000\&from\_asset=BTC.BTC\&to\_asset=ETH.ETH\&destination=0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430\&affiliate=thorname\&affiliate\_bps=10](https://thornode.ninerealms.com/thorchain/quote/swap?amount=100000000\&from\_asset=BTC.BTC\&to\_asset=ETH.ETH\&destination=0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430\&affiliate=thorname\&affiliate\_bps=10)

```json
{
  "dust_threshold": "10000",
  "expected_amount_out": "1603383828",
  "expiry": 1688973775,
  "fees": {
    "affiliate": "1605229",
    "asset": "ETH.ETH",
    "outbound": "240000"
  },
  "inbound_address": "bc1qhkutxeluztncm5pq0ckpm75hztrv7m7nhhh94d",
  "inbound_confirmation_blocks": 1,
  "inbound_confirmation_seconds": 600,
  "max_streaming_quantity": 0,
  "memo": "=:ETH.ETH:0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430::thorname:10",
  "notes": "First output should be to inbound_address, second output should be change back to self, third output should be OP_RETURN, limited to 80 bytes. Do not send below the dust threshold. Do not use exotic spend scripts, locks or address formats (P2WSH with Bech32 address format preferred).",
  "outbound_delay_blocks": 303,
  "outbound_delay_seconds": 1818,
  "recommended_min_amount_in": "72000",
  "slippage_bps": 49,
  "streaming_swap_blocks": 0,
  "total_swap_seconds": 2418,
  "warning": "Do not cache this response. Do not send funds after the expiry."
}
```

Notice how `thorname:10` has been appended to the end of the memo. This instructs THORChain to skim 10 basis points from the swap. The user should still expect to receive the _expected\_amount\_out,_ meaning the affiliate fee has already been subtracted from this number.

For more information on affiliate fees: [fees.md](../concepts/fees.md "mention").

### Streaming Swaps

[_Streaming Swaps_](../concepts/streaming-swaps.md) _can be used to break up the trade to reduce slip fees._&#x20;

Specify `streaming_interval` to define the interval in which streaming swaps are swapped.

[_https://stagenet-thornode.ninerealms.com/thorchain/quote/swap?amount=100000000\&from\_asset=BTC.BTC\&to\_asset=ETH.ETH\&destination=0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430\&streaming\_interval=10_](https://stagenet-thornode.ninerealms.com/thorchain/quote/swap?amount=100000000\&from\_asset=BTC.BTC\&to\_asset=ETH.ETH\&destination=0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430\&streaming\_interval=10)

```json
{
  "approx_streaming_savings": 0.99930555,
  "dust_threshold": "10000",
  "expected_amount_out": "145448080",
  "expiry": 1689117597,
  "fees": {
    "affiliate": "0",
    "asset": "ETH.ETH",
    "outbound": "480000"
  },
  "inbound_address": "bc1qk2z8luw2afwuugndynegn72dkv45av5hyjrtm8",
  "inbound_confirmation_blocks": 1,
  "inbound_confirmation_seconds": 600,
  "max_streaming_quantity": 1440,
  "memo": "=:ETH.ETH:0x3021c479f7f8c9f1d5c7d8523ba5e22c0bcb5430:0/10/1440",
  "notes": "First output should be to inbound_address, second output should be change back to self, third output should be OP_RETURN, limited to 80 bytes. Do not send below the dust threshold. Do not use exotic spend scripts, locks or address formats (P2WSH with Bech32 address format preferred).",
  "outbound_delay_blocks": 76,
  "outbound_delay_seconds": 456,
  "recommended_min_amount_in": "158404",
  "slippage_bps": 8176,
  "streaming_swap_blocks": 14400,
  "streaming_swap_seconds": 86400,
  "total_swap_seconds": 87456,
  "warning": "Do not cache this response. Do not send funds after the expiry."
}
```

Notice how `approx_streaming_savings` shows the savings by using streaming swaps. `total_swap_seconds` also shows the amount of time the swap will take.&#x20;

### Error Handling

The quote swap endpoint simulates all of the logic of an actual swap transaction. It ships with comprehensive error handling.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption><p>This error means the swap cannot be completed given your price tolerance.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>This error ensures the destination address is for the chain specified by to_asset.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>This error is due to the fact the affiliate address is too long given the source chain's memo length requirements. Try registering a THORName to shorten the memo.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>This error means the requested asset does not exist.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>Bound checks are made on both affiliate_<em>bps</em> and tolerance_bps.</p></figcaption></figure>

### Support

Developers experiencing issues with these APIs can go to the [Developer Discord](https://discord.gg/2Vw3RsQ7) for assistance. Interface developers should subscribe to the #interface-alerts channel for information pertinent to the endpoints and functionality discussed here.
