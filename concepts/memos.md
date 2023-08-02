---
description: Transaction Memo Details
---

# Transaction Memos

### Overview

Transactions to THORChain pass user-intent with the `MEMO` field on their respective chains. THORChain inspects the transaction object as well as the `MEMO` in order to process the transaction, so care must be taken to ensure the `MEMO` and the transaction are both valid. If not, THORChain will automatically refund the assets. All valid memos are listed [here](https://gitlab.com/thorchain/thornode/-/blob/develop/x/thorchain/memo/memo.go#L41).

Different chains have different ways of adding state to a transaction. Long assets can be shortened using Asset abbreviations (below) as well as THORNames to reduce the size of destination/affiliate addresses.

### Format

Memos follow the format:

`FUNCTION:PARAM1:PARAM2:PARAM3:PARAM4`

The function is invoked by a string, which in turn calls a particular handler in the state machine. The state machine parses the memo looking for the parameters which it simply decodes from human-readable strings.&#x20;

In addition, some parameters are optional. Simply leave them blank, but retain the `:` separator:

`FUNCTION:PARAM1:::PARAM4`

### Permitted Memos

The following memos are permitted:

1. [**SWAP**](memos.md#swap)
2. [**Deposit**](memos.md#swap-1) Savers
3. [**Withdraw** ](memos.md#swap-2)Savers
4. [**ADD** ](memos.md#swap-3)Liquidity
5. [**WITHDRAW**](memos.md#withdraw-liquidity) Liquidity
6. [**BOND**, **UNBOND** & **LEAVE**](memos.md#bond-unbond-and-leave)
7. [**DONATE** & **RESERVE**](memos.md#donate-and-reserve)
8. [**NOOP**](memos.md#noop)

### Swap

Perform a swap.&#x20;

**`SWAP:ASSET:DESTADDR:LIM/INTERVAL/QUANTITY:AFFILIATE:FEE`**

<table><thead><tr><th width="156.4771610766654">Parameter</th><th width="338.89853142855793">Notes</th><th>Conditions</th></tr></thead><tbody><tr><td>Payload</td><td>Send the asset to swap. </td><td>Must be an active pool on THORChain.</td></tr><tr><td><code>SWAP</code></td><td>The swap handler </td><td>Also <code>s</code>, <code>=</code></td></tr><tr><td><code>:ASSET</code></td><td>The asset identifier.</td><td>Can be shortened.</td></tr><tr><td><code>:DESTADDR</code></td><td>The destination address to send to. </td><td>Can use THORName.</td></tr><tr><td><code>:LIM</code> </td><td>The trade limit ie, set 100000000 to get a minimum of 1 full asset, else a refund.</td><td>Optional, 1e8 format</td></tr><tr><td><code>/INTERVAL</code></td><td>Swap interval ncy in blocks</td><td>Optional, 1 means do not stream</td></tr><tr><td><code>/QUANTITY</code></td><td>Swap Quantity. Swap interval times every Interval blocks.</td><td>Optional, if 0, network will determine the number of swaps</td></tr><tr><td><code>:AFFILIATE</code></td><td>The affiliate address. RUNE is sent to Affiliate. </td><td>Optional. Must be THORName or THOR Address.</td></tr><tr><td><code>:FEE</code></td><td>The affiliate fee. Limited from 0 to 1000 Basis Points. </td><td>Optional</td></tr></tbody></table>

**Examples**

**`SWAP:ASSET:DESTADDR`** simply swap

**`=:ASSET:DESTADDR:LIM`** swap with limit

**`s:ASSET:DESTADDR:LIM:AFFILIATE:FEE`** swap with limit and affiliate

**`=:ASSET:DESTADDR:LIM/1/1`** swap with limit, do not steam swap

**`=:ASSET:DESTADDR:LIM/1`** swap with limit, optimise swap

**`=:ASSET:DESTADDR:LIM:/1/0:AFFILIATE:FEE`** swap with limit, optimised and affiliate fee

`=:THOR.RUNE:thor1el4ufmhll3yw7zxzszvfakrk66j7fx0tvcslym:19779138111`

`=:THOR.RUNE:thor1el4ufmhll3yw7zxzszvfakrk66j7fx0tvcslym:0/2/5`

`s:BNB/BUSD-BD1:thor15s4apx9ap7lazpsct42nmvf0t6am4r3w0r64f2:628197586176`

`=:BNB.BNB:bnb108n64knfm38f0mm23nkreqqmpc7rpcw89sqqw5:5440167/2/6` six swaps, every two blocks.

### Deposit Savers <a href="#swap" id="swap"></a>

Depositing savers can work without a memo however memos are recommended to be explicit about the transaction intent.&#x20;

| Paramater | Notes                             | Conditions                     |
| --------- | --------------------------------- | ------------------------------ |
| Payload   | The asset to add liquidity with.  | Must be supported by THORChain |
| ADD       | The Deposit handler.              | Also `a` `+`                   |
| `:POOL`   | The pool to add liquidity to.     | Gas pools only.                |

**Examples**\
`+:BTC/BTC` add to the BTC Savings Vault

`a:ETH/ETH` add to the ETH Savings Vault

### **Withdraw Savers** <a href="#swap" id="swap"></a>

| Paramater      | Notes                                                                                                                                                         | Extra                                                                                                                                                      |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Payload        | Send the [dust threshold](https://midgard.ninerealms.com/v2/thorchain/inbound\_addresses) of the asset to cause the transaction to be picked up by THORChain. | Caution [Dust Limits](https://midgard.ninerealms.com/v2/thorchain/inbound\_addresses): BTC,BCH,LTC chains 10k sats; DOGE 1m Sats; ETH 0 wei; THOR 0 RUNE.  |
| `WITHDRAW`     | The withdraw handler.                                                                                                                                         | Also `-` `wd`                                                                                                                                              |
| `:POOL`        | The pool to withdraw liquidity from.                                                                                                                          | Gas pools only.                                                                                                                                            |
| `:BASISPOINTS` | Basis points (0-10000, where 10000=100%)                                                                                                                      |                                                                                                                                                            |

**Examples**

`-:BTC/BTC:10000` Withdraw 100% from BTC Savers

`w:ETH/ETH:5000` Withdraw 50% from ETH Savers

### Add Liquidity <a href="#swap" id="swap"></a>

There are rules for adding liquidity, see [the rules here](https://docs.thorchain.org/learn/getting-started#entering-and-leaving-a-pool).\
**`ADD:POOL:PAIREDADDR:AFFILIATE:FEE`**

<table><thead><tr><th width="178.1692373378876">Parameter</th><th width="319.5924083090204">Notes</th><th>Conditions</th></tr></thead><tbody><tr><td>Payload</td><td>The asset to add liquidity with. </td><td>Must be supported by THORChain.</td></tr><tr><td><code>ADD</code></td><td>The Add Liquidity handler.</td><td>also <code>a</code> <code>+</code></td></tr><tr><td><code>:POOL</code></td><td>The pool to add liquidity to.</td><td>Can be shortened.</td></tr><tr><td><code>:PAIREDADDR</code></td><td>The other address to link with. If on external chain, link to THOR address. If on THORChain, link to external address. If a paired address is found, the LP is matched and added. If none is found, the liquidity is put into pending. </td><td>Optional. If not specified, a single-sided add-liquidity action is created. </td></tr><tr><td><code>:AFFILIATE</code></td><td>The affiliate address. The affiliate is added in to the pool as an LP. </td><td>Optional. Must be THORName or THOR Address.</td></tr><tr><td><code>:FEE</code></td><td>The affiliate fee. Fee is allocated to the affiliate. </td><td>Optional. Limited from 0 to 1000 Basis Points.</td></tr></tbody></table>

**Examples**

**`ADD:POOL`** single-sided add liquidity.  If this is a position's first add, liquidity can only be withdrawn to the same address.

**`+:POOL:PAIREDADDR`** add on both sides.&#x20;

**`a:POOL:PAIREDADDR:AFFILIATE:FEE`** add with affiliate

`+:BTC.BTC:`

### Withdraw Liquidity

Withdraw liquidity from a pool.\
A withdrawal can be either dual-sided (wtihdrawn based on pool's price) or entirely single-sided (converted to one side and sent out).

**`WITHDRAW:POOL:BASISPOINTS:ASSET`**

<table><thead><tr><th width="209">Parameter</th><th width="305">Notes</th><th>Extra</th></tr></thead><tbody><tr><td>Payload</td><td>Send the dust threshold of the asset to cause the transaction to be picked up by THORChain. </td><td>Caution <a href="https://midgard.ninerealms.com/v2/thorchain/inbound_addresses">Dust Limits</a>: BTC,BCH,LTC chains 10k sats; DOGE 1m Sats; ETH 0 wei; THOR 0 RUNE. </td></tr><tr><td><code>WITHDRAW</code></td><td>The withdraw handler.</td><td>Also <code>-</code> <code>wd</code></td></tr><tr><td><code>:POOL</code></td><td>The pool to withdraw liquidity from.</td><td>Can be shortened.</td></tr><tr><td><code>:BASISPOINTS</code></td><td>Basis points (0-10000, where 10000=100%)</td><td></td></tr><tr><td><code>:ASSET</code></td><td>Single-sided withdraw to one side.</td><td>Optional. Can be shortened. Must be either RUNE or the ASSET.</td></tr></tbody></table>

**Examples**

**`WITHDRAW:POOL:10000`** dual-sided 100% withdraw liquidity.  If a single-address position, this withdraws single-sidedly instead.

**`-:POOL:1000`** dual-sided 10% withdraw liquidity.&#x20;

**`wd:POOL:5000:ASSET`** withdraw 50% liquidity as the asset specified while the rest stays in the pool, eg:

`wd:BTC.BTC:5000:BTC.BTC`

### DONATE & RESERVE

Donate to a pool or the RESERVE.

**`DONATE:POOL`**

<table><thead><tr><th width="153.3006301940066">Parameter</th><th width="150">Notes</th><th>Extra</th></tr></thead><tbody><tr><td>Payload</td><td>The asset to donate to a THORChain pool.</td><td>Must be supported by THORChain. Can be RUNE or ASSET.</td></tr><tr><td><code>DONATE</code></td><td>The donate handler.</td><td>Also <code>%</code></td></tr><tr><td><code>:POOL</code></td><td>The pool to withdraw liquidity from.</td><td>Can be shortened.</td></tr></tbody></table>

**`RESERVE`**

<table><thead><tr><th width="154.85927710812584">Parameter</th><th width="187.65003293833834">Notes</th><th>Extra</th></tr></thead><tbody><tr><td>Payload</td><td>THOR.RUNE</td><td>The RUNE to credit to the RESERVE.</td></tr><tr><td><code>RESERVE</code></td><td>The reserve handler.</td><td></td></tr></tbody></table>

### BOND, UNBOND & LEAVE

Perform node maintenance features.&#x20;

**`BOND:NODEADDR:PROVDER:FEE`**

<table><thead><tr><th width="256.36066472429746">Parameter</th><th width="150">Notes</th><th>Extra</th></tr></thead><tbody><tr><td>Payload</td><td>The asset to bond to a  Node.</td><td>Must be RUNE.</td></tr><tr><td><code>BOND</code></td><td>The bond handler.</td><td>Anytime. </td></tr><tr><td><code>:NODEADDR</code></td><td>The node to bond with.</td><td></td></tr><tr><td><code>:PROVIDER</code></td><td>Whitelist in a provider.</td><td>Optional, add a provider</td></tr><tr><td><code>:FEE</code></td><td>Specify an Operator Fee in Basis Points.</td><td>Optional, default will be the mimir value (2000 Basis Points). Can be changed anytime. </td></tr></tbody></table>

**`UNBOND:NODEADDR:AMOUNT`**

<table><thead><tr><th width="153.3006301940066">Parameter</th><th width="150">Notes</th><th>Extra</th></tr></thead><tbody><tr><td>Payload</td><td>None required</td><td>Use <code>MsgDeposit</code></td></tr><tr><td><code>UNBOND</code></td><td>The unbond handler.</td><td></td></tr><tr><td><code>:NODEADDR</code></td><td>The node to unbond from.</td><td>Must be in standby only.</td></tr><tr><td><code>:AMOUNT</code></td><td>The amount to unbond.</td><td>In 1e8 format. If setting more than actual bond, then capped at bond. </td></tr></tbody></table>

**`LEAVE:NODEADDR`**

<table><thead><tr><th width="153.3006301940066">Parameter</th><th width="150">Notes</th><th>Extra</th></tr></thead><tbody><tr><td>Payload</td><td>None required</td><td>Use <code>MsgDeposit</code></td></tr><tr><td><code>LEAVE</code></td><td>The leave handler.</td><td></td></tr><tr><td><code>:NODEADDR</code></td><td>The node to force to leave.</td><td>If in Active, request a churn out to Standby for 1 churn cycle. If in Standby, forces a permanent leave. </td></tr></tbody></table>

**Examples**

`BOND:thor1xd4j3gk9frpxh8r22runntnqy34lwzrdkazldh`

`LEAVE:thor18r8gnfm4qjak47qvpjdtw66ehsx49w99c5wewd`

### NOOP

Dev-centric functions to fix THORChain state. Caution: may cause loss of funds if not done exactly right at the right time.&#x20;

**`NOOP`**

<table><thead><tr><th width="204.85088245272618">Parameter</th><th width="150">Notes</th><th>Extra</th></tr></thead><tbody><tr><td>Payload</td><td>The asset to credit to a vault.</td><td>Must be ASSET or RUNE.</td></tr><tr><td><code>NOOP</code></td><td>The  noop handler.</td><td>Adds to the vault balance, but does not add to the pool.</td></tr><tr><td><code>:NOVAULT</code></td><td>Do not credit the vault.</td><td>Optional. Just fix the insolvency issue.</td></tr></tbody></table>

### Refunds

The following are the conditions for refunds:

| Condition                | Notes                                                                                                        |
| ------------------------ | ------------------------------------------------------------------------------------------------------------ |
| Invalid `MEMO`           | If the `MEMO` is incorrect the user will be refunded.                                                        |
| Invalid Assets           | If the asset for the transaction is incorrect (adding an asset into a wrong pool) the user will be refunded. |
| Invalid Transaction Type | If the user is performing a multi-send vs a send for a particular transaction, they are refunded.            |
| Exceeding Price Limit    | If the final value achieved in a trade differs to expected, they are refunded.                               |

Refunds cost fees to prevent Denial of Service attacks. The user will pay the correct outbound fee for that chain.

## Asset Notation

The following is the notation for Assets in THORChain's system:

![](https://docs.google.com/drawings/u/1/d/skidhZPIsMKQ-XWJWb3EJaQ/image?w=698\&h=276\&rev=23\&ac=1\&parent=1ZoJQKvyATQekFbWMk\_rqX96K9BSmCArh9e-A\_g66wDQ)

#### Note: CHAIN.ASSET denotes native asset. CHAIN/ASSET denotes a Synthetic Asset

#### Examples

| Asset         | Notation                                            |
| ------------- | --------------------------------------------------- |
| Bitcoin       | BTC.BTC (Native)                                    |
| Bitcoin       | BTC/BTC (Synth)                                     |
| Ethereum      | ETH.ETH                                             |
| USDT          | ETH.USDT-0xdac17f958d2ee523a2206206994597c13d831ec7 |
| BNB           | BNB.BNB (Native)                                    |
| BNB           | BNB/BNB (Synth)                                     |
| RUNE (BEP2)   | BNB.RUNE-B1A                                        |
| RUNE (NATIVE) | THOR.RUNE                                           |

### **Shortened Asset Names**

Asset names can be shortened to reduce the size of the memo.

| Shorten Asset | Asset Notation |
| ------------- | -------------- |
| a             | AVAX.AVAX      |
| b             | BTC.BTC        |
| c             | BCH.BCH        |
| n             | BNB.BNB        |
| s             | BSC.BNB        |
| d             | DOGE.DOGE      |
| e             | ETH.ETH        |
| l             | LTC.LTC        |
| r             | THOR.RUNE      |

Example Swaps:

`=:e:0x388C818CA8B9251b393131C08a736A67ccB19297`

`=:r:thor1el4ufmhll3yw7zxzszvfakrk66j7fx0tvcslym`

### Asset Abbreviations

Assets can be abbreviated using fuzzy logic. The following will all be matched appropriately. If there are conflicts then the deepest pool is matched. (To prevent attacks).

| Notation                                            |
| --------------------------------------------------- |
| ETH.USDT                                            |
| ETH.USDT-ec7                                        |
| ETH.USDT-6994597c13d831ec7                          |
| ETH.USDT-0xdac17f958d2ee523a2206206994597c13d831ec7 |

### Reducing Memo Size <a href="#mechanism-for-transaction-intent" id="mechanism-for-transaction-intent"></a>

Using the [Shortened Asset Names](memos.md#shortened-asset-names), [Asset Abbreviations](memos.md#asset-abbreviations) and [THORNames](http://127.0.0.1:5000/o/-Lkfu3JTXBEIKxOI-QAF/s/-Lkfu6B\_aaimdwQoqPhW/) along with 1e8 format, memos can be greatly reduced.

**Examples:**&#x20;

**Full Memo:** `SWAP:ETH/ETH:0xe6a30f4f3bad978910e2cbb4d97581f5b5a0ade0:1612345678:thor1el4ufmhll3yw7zxzszvfakrk66j7fx0tvcslym:10`

**Reduced Memo:**&#x20;

`=:e:userthorname:161e6:affthorname:10`

Swap to Ethereum with a minimum output of 16.1 Ether. Deduct 10 basis points from the input and send it to `addthorname`. Send the Ether output to `userthorname.`

**Full Memo**

`SWAP:ETH.USDT-0xdac17f958d2ee523a2206206994597c13d831ec7:0xe6a30f4f3bad978910e2cbb4d97581f5b5a0ade0:10012345678:thor1el4ufmhll3yw7zxzszvfakrk66j7fx0tvcslym:10`\
**Reduced Memo**

`=:ETH.USDT:userthorname:100e7:affthorname:10`

### The Mechanism for Transaction Intent <a href="#mechanism-for-transaction-intent" id="mechanism-for-transaction-intent"></a>

<table><thead><tr><th>Chain</th><th width="208.66666666666666">Mechanism</th><th>Notes</th></tr></thead><tbody><tr><td>Bitcoin</td><td>OP_RETURN</td><td>Limited to 80 bytes.</td></tr><tr><td>Ethereum</td><td>Smart Contract Input</td><td>Use <code>deposit(vault, asset, amount, memo)</code> function, where <code>memo</code> is string</td></tr><tr><td>Binance Chain</td><td>MEMO</td><td>Each transaction has an optional memo, limited to 128 bytes.</td></tr><tr><td>Monero</td><td>Extra Data</td><td>Each transaction can have attached <code>extra data</code> field, that has no limits.</td></tr></tbody></table>

Each chain will have a unique way of adding state to a transaction. Long assets can be shortened using Asset abbreviations (below) as well as THORNames to reduce the size of destination/affiliate addresses.​
