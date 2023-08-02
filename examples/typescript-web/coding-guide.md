# Coding Guide

A coding overview to xchainjs.

### **General**

The foundation of xchainjs is defined in the [xchain-util](https://github.com/xchainjs/xchainjs-lib/tree/master/packages/xchain-util) package

* `Address`: a crypto address as a string.
* `BaseAmount`:  a bigNumber in 1e8 format. E.g. 1 BTC = 100,000,000 in BaseAmount
* `AssetAmount`: a BaseAmount\*10^8. E.g. 1 BTC = 1 in Asset Amount.&#x20;
* `Asset`: Asset details {Chain, symbol, ticker, isSynth}

{% hint style="info" %}
All `Assets` must conform to the [Asset Notation](../../concepts/memos.md#asset-notation)

`assetFromString()` is used to quickly create assets and will assign chain and synth.
{% endhint %}

* `CryptoAmount:` is a class that has:

```
 baseAmount: BaseAmount
 readonly asset: Asset
```

All crypto should use the `CryptoAmount` object with the understanding they are in BaseAmount format. An example to switch between them:

```typescript
// Define 1 BTC as CryptoAmount
oneBtc = new CryptoAmount(assetToBase(assetAmount(1)), assetFromStringEx(`BTC.BTC`))
// Print 1 BTC in BaseAmount 
console.log(oneBtc.amount().toNumber().toFixed()) // 100000000
// Print 1 BTC in Asset Amount 
console.log(oneBtc.AssetAmount().amount().toNumber().toFixed()) // 1
```

## Query

Major data types for the thorchain-query package.&#x20;

* [Package description](query-package.md)
* [Github source code](https://github.com/xchainjs/xchainjs-lib/tree/master/packages/xchain-thorchain-query)
* [Code examples](query-package.md#code-examples-in-replit)
* [Install procedures](overview.md#install-procedures)

### **EstimateSwapParams**

The input Type for `estimateSwap`. This is designed to be created by interfaces and passed into EstimateSwap. Also see [Swap Memo](../../concepts/memos.md#swap) for more information.&#x20;

<table><thead><tr><th>Variable</th><th width="152">Data Type</th><th>Comments</th></tr></thead><tbody><tr><td>input</td><td>CryptoAmount</td><td>inbound asset and amount</td></tr><tr><td>destinationAsset</td><td>Asset</td><td>outbound asset</td></tr><tr><td>destinationAddress</td><td>String</td><td>outbound asset address</td></tr><tr><td>slipLimit</td><td>BigNumber</td><td>Optional: Used to set LIM</td></tr><tr><td>affiliateFeePercent</td><td>number</td><td>Optional: 0-0.1 allowed</td></tr><tr><td>affiliateAddress</td><td>Address</td><td>Optional: thor address</td></tr><tr><td>interfaceID</td><td>string</td><td>Optional: assigned interface ID</td></tr></tbody></table>

### **SwapEstimate**

The internal return type is used within estimateSwap after the calculation is done.&#x20;

<table><thead><tr><th width="269">Variable</th><th width="148">Data Type</th><th>Comments</th></tr></thead><tbody><tr><td>totalFees</td><td>TotalFees</td><td>All fees for swap</td></tr><tr><td>slipPercentage</td><td>BigNumber</td><td>Actual slip of the swap</td></tr><tr><td>netOutput</td><td>CryptoAmount</td><td>input - totalFees</td></tr><tr><td>waitTimeSeconds</td><td>number</td><td>Estimated time for the swap</td></tr><tr><td>canSwap</td><td>boolean</td><td>false if there is an issue</td></tr><tr><td>errors</td><td>string array</td><td>contains info if canSwap is false</td></tr></tbody></table>

### **TxDetails**

Return type of `estimateSwap`. This is designed to be used by interfaces to give them all the information they need to display to the user.&#x20;

<table><thead><tr><th width="245">Variable</th><th width="141">Data Type</th><th>Comments</th></tr></thead><tbody><tr><td>txEstimate</td><td>SwapEstimate</td><td>swap details</td></tr><tr><td>memo</td><td>string</td><td>constructed memo THORChain will understand</td></tr><tr><td>expiry</td><td>DateTime</td><td>when the SwapEstimate information will no longer be valid.</td></tr><tr><td>toAddress</td><td>string</td><td>current Asgard Vault address From inbound_address</td></tr></tbody></table>

{% hint style="danger" %}
Do not use `toAddress` after `expiry` as the Asgard vault rotates
{% endhint %}

## AMM

Major data types for the thorchain-query package.&#x20;

* [Package description](amm-package.md)
* [Github source code](https://github.com/xchainjs/xchainjs-lib/tree/master/packages/xchain-thorchain-amm)
* [Code examples](amm-package.md#code-examples-in-replit)
* [Install procedures](overview.md#install-procedures)

### **ExecuteSwap**

Input Type for doSwap where a swap will be actually conducted. Based on EstimateSwapParams.

### **TxSubmitted**

| Variable        |        |                             |
| --------------- | ------ | --------------------------- |
| hash            | string | inbound Tx Hash             |
| url             | string | Block exploer url           |
| waitTimeSeconds | number | Estimated time for the swap |
