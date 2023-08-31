---
description: Requests a deposit address where funds to be swapped are sent
---

# Request Deposit Address

The ability to create a unique deposit address reserved for a user during a fixed period of time provides flexibility — no need to connect a wallet — and is a unique feature of the Chainflip protocol.

Once the deposit address is available, the user can send the funds to trigger the swap process.

{% hint style="info" %}
Learn more about creating channels in[Deposit Channels & Brokers](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/deposit-channels-and-brokers "mention") docs.
{% endhint %}

## requestDepositAddress

Requests a deposit address based on the provided `swapRequest`.&#x20;

```typescript
requestDepositAddress(
  depositAddressRequest: SwapRequest, 
  options?: RequestOptions
): Promise<SwapResponse>
```

The `depositAddressRequest` object describes the swap for which a deposit address is requested.&#x20;

<table><thead><tr><th width="223">Param</th><th width="371">Description</th><th width="244.33333333333331">Data type</th></tr></thead><tbody><tr><td><code>srcChain</code></td><td>Source chain for the swap</td><td>string</td></tr><tr><td><code>destChain</code></td><td>Destination chain for the swap</td><td>string</td></tr><tr><td><code>srcAsset</code></td><td>Symbol of the token to be swapped from the source chain</td><td>string</td></tr><tr><td><code>destAsset</code></td><td>Symbol of the token to be received on the destination chain</td><td>string</td></tr><tr><td><code>destAddress</code></td><td>Address where the swapped tokens will be sent to on the destination chain</td><td>string</td></tr><tr><td><code>amount</code></td><td>Amount of the source token to be swapped</td><td>string</td></tr><tr><td><code>ccmMetadata</code></td><td>Optional metadata for triggering a smart contract call on the destination chain.</td><td>object</td></tr></tbody></table>

### Example

```typescript
import { Chains, Assets } from '@chainflip-io/chainflip-sdk/swap';

const swapDepositAddressRequest = {
  srcChain: Chains.Ethereum,
  destChain: Chains.Bitcoin,
  srcAsset: Assets.ETH,
  destAsset: Assets.BTC,
  destAddress: 'bc1qar0srrr7xfkvy5l643lydnw9re59gtzzwf5mdq',
  amount: (15e17).toString(), // 1.5 ETH
};
console.log(await swapSDK.requestDepositAddress(swapDepositAddressRequest));
```

The `amount` will always be in the **base unit of the source asset**, i.e. for `ETH` it will be **Wei**.&#x20;

### Sample Response

```json
{
  "srcChain": "Ethereum",
  "destChain": "Bitcoin",
  "srcAsset": "ETH",
  "destAsset": "BTC",
  "destAddress": "bc1qar0srrr7xfkvy5l643lydnw9re59gtzzwf5mdq",
  "amount": (15e17).toString(), // 1.5 ETH
  "depositChannelId": "1234567890", // Identifies the deposit channel for this swap
  "depositAddress": "0x1234567890abcdef1234567890abcdef12345678" // Address where funds need to be deposited to start the swap
}
```

## requestDepositAddress + Cross-Chain Messaging (CCM)

The optional `ccmMetadata` object enables **executing a smart contract call** on the destination chain (see [#executecall](execute-swap.md#executecall "mention")) and has the following properties:

<table><thead><tr><th width="223">Param</th><th width="371">Description</th><th width="244.33333333333331">Data type</th></tr></thead><tbody><tr><td><code>message</code></td><td>Message that is passed to the destination address on the destination chain</td><td>string</td></tr><tr><td><code>gasBudget</code></td><td>Gas budget for the call on the destination chain. This amount is based on the source asset and will be swapped to pay for gas.</td><td>number</td></tr></tbody></table>

### Example

In this example, we're including `ccmMetadata` in the request:

```typescript
import { Chains, Assets } from '@chainflip-io/chainflip-sdk/swap';

const callDepositAddressRequest = {
  srcChain: Chains.Bitcoin,
  destChain: Chains.Ethereum,
  srcAsset: Assets.BTC,
  destAsset: Assets.ETH,
  destAddress: '0x2f41dd5dEe1BcF767139b6bB6e27673aE90061b5',
  amount: (1e8).toString(), // 1 BTC
  ccmMetadata: {
    message: '0xdeadc0de',
    gasBudget: (1e5).toString(), // 0.001 BTC will be swapped for ETH to pay for gas
  },
};
console.log(await swapSDK.requestDepositAddress(callDepositAddressRequest));
```

The `amount` and `gasBudget` will always be in the **base unit of the source asset**, i.e. for `ETH` it will be **Wei**.&#x20;

### Sample Response

```json
{
  "srcChain": "Bitcoin",
  "destChain": "Ethereum",
  "srcAsset": "BTC",
  "destAsset": "ETH",
  "destAddress": "0x2f41dd5dEe1BcF767139b6bB6e27673aE90061b5",
  "amount": (1e8).toString(), // 1 BTC
  "depositChannelId": "1234567890", // Identifies the deposit channel for this swap
  "depositAddress": "tb1pylj9uhsmuz7h62spprv2z2vcnx2lw9epzt4amm3j45y75r6rrd8sdx0sjf", // Address where funds need to be deposited to start the swap
  "ccmMetadata": {
    "message": '0xdeadc0de',
    "gasBudget": (1e5).toString(), // 0.001 BTC will be swapped for ETH to pay for gas
  },
}
```

The resulting `depositChannelId` can be used in [get-status.md](../get-status.md "mention").
