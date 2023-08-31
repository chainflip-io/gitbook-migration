---
description: Requests a price quote for a given swap
---

# Get Quote

### getQuote

Fetches a quote for swapping tokens based on the provided `getQuote` and an `options` argument.

```typescript
getQuote(
  quoteRequest: QuoteRequest, 
  options?: RequestOptions
): Promise<QuoteResponse>
```

The `quoteRequest` object describes the swap for which a quote is returned.

<table><thead><tr><th width="241">Param</th><th width="372">Description</th><th width="244.33333333333331">Data type</th></tr></thead><tbody><tr><td><code>srcChain</code></td><td>Source chain for the swap</td><td>ChainId</td></tr><tr><td><code>destChain</code></td><td>Destination chain for the swap</td><td>ChainId</td></tr><tr><td><code>srcAsset</code></td><td>Asset to be swapped from the source chain</td><td>string</td></tr><tr><td><code>destAsset</code></td><td>Asset to be received on the destination chain</td><td>string</td></tr><tr><td><code>amount</code></td><td>Amount of the source token to be swapped</td><td>string</td></tr></tbody></table>

### Example

```typescript
import { Chains, Assets } from '@chainflip-io/chainflip-sdk/swap';

const quoteRequest = {
  srcChain: Chains.Ethereum,
  destChain: Chains.Bitcoin,
  srcAsset: Assets.ETH,
  destAsset: Assets.BTC,
  amount: '1500000000000000000' // 1.5 ETH
};

console.log(await swapSDK.getQuote(quoteRequest));

```

### Sample Response

```json
{
  "srcChain": "Ethereum",
  "destChain": "Bitcoin",
  "srcAsset": "ETH",
  "destAsset": "BTC",
  "amount": "1500000000000000000", // 1.5 ETH
  "quote": {
    "intermediateAmount": "2000000000", // 2000 USDC
    "egressAmount": "10000000", // 0.1 BTC
  }
}
```

{% hint style="info" %}
The intermediate amount is the value of the first swap leg, when the swap requires more than a pool. In this case, BTC -> ETH requires BTC/USDC and USDC/ETH pools.&#x20;

Learn more about [$USDC Denominated Pools](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/usdusdc-denominated-pools "mention").&#x20;
{% endhint %}
