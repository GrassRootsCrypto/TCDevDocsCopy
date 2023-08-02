---
description: >-
  This page goes over how to build an inbound THORChain transaction for each
  chain type.
---

# Sending Transactions

Confirm you have:

* [ ] Connected to Midgard or THORNode
* [ ] Located the latest vault (and router) for the chain
* [ ] Prepared the transaction details (and memo)
* [ ] Checked the network is not halted for your transaction

You are ready to make the transaction and swap via THORChain.&#x20;

### UTXO Chains

* [ ] Send the transaction with Asgard vault as VOUT0
* [ ] Include the memo as an OP\_RETURN in VOUT1
* [ ] Pass all change back to the VIN0 address in VOUT3
* [ ] Use a high enough `gas_rate` to be included
* [ ] Do not send below the dust threshold (10k Sats BTC, BCH, LTC, 1m DOGE), exhaustive values can be found on the [Inbound Addresses](https://thornode.ninerealms.com/thorchain/inbound\_addresses) endpoint&#x20;

{% hint style="warning" %}
Inbound transactions should not be delayed for any reason else there is risk funds will be sent to an unreachable address. Use standard transactions, check the [`Inbound_Address`](querying-thorchain.md#getting-the-asgard-vault) before sending and use the recommended [`gas rate`](querying-thorchain.md#getting-the-asgard-vault) to ensure transactions are confirmed in the next block to the latest `Inbound_Address`.
{% endhint %}

{% hint style="info" %}
Memo limited to 80 bytes on BTC. Use abbreviated options and [THORNames](https://docs.thorchain.org/network/thorchain-name-service) where possible.
{% endhint %}

{% hint style="warning" %}
Do not use HD wallets that forward the change to a new address, because THORChain IDs the user as the address in VIN0. The user must keep their VIN0 address funded for refunds.
{% endhint %}

{% hint style="danger" %}
Override randomised VOUT ordering; THORChain requires specific output ordering.&#x20;
{% endhint %}

### EVM Chains

{% embed url="https://gitlab.com/thorchain/ethereum/eth-router/-/blob/master/contracts/THORChain_Router.sol#L66" %}

```
depositWithExpiry(vault, asset, amount, memo, expiry)
```

* [ ] If ERC20, approve the router to spend an allowance of the token first
* [ ] Send the transaction as a `depositWithExpiry()` on the router
* [ ] Vault is the Asgard vault address, asset is the token address to swap, memo as a string
* [ ] Use an expiry which is +60mins on the current time (if the tx is delayed, it will get refunded)
* [ ] Use a high enough `gas_rate` to be included, otherwise the tx will get stuck

{% hint style="info" %}
ETH is `0x0000000000000000000000000000000000000000`
{% endhint %}

{% hint style="danger" %}
ETH is sent and received as an internal transaction. Your wallet may not be set to read internal balances and transactions
{% endhint %}

### BFT Chains

* [ ] Send the transaction to the Asgard vault
* [ ] Include the memo
* [ ] Only use the base asset as the choice for gas asset

## THORChain

To initiate a $RUNE -> $ASSET swap a `MsgDeposit` must be broadcasted to the THORChain blockchain. The `MsgDeposit` does not have a destination address, and has the following properties. The full definition can be found [here](https://gitlab.com/thorchain/thornode/-/blob/develop/x/thorchain/types/msg\_deposit.go).

```go
MsgDeposit{
    Coins:  coins,
    Memo:   memo,
    Signer: signer,
}
```

If you are using Javascript, [CosmJS](https://github.com/cosmos/cosmjs) is the recommended package to build and broadcast custom message types. [Here is a walkthrough](https://github.com/cosmos/cosmjs/blob/main/packages/stargate/CUSTOM\_PROTOBUF\_CODECS.md).&#x20;
