---
description: Uses smart contract calls to execute a swap
---

# Execute Swap

Alternatively, calling the `swap()` function in the Chainflip Vault contracts allows to initiate a swap directly. The swap is then triggered without the need of [requesting a Deposit Address](request-deposit-address.md) and therefore, no [Broker](../../running-a-broker/#how-brokers-work) is necessary.

{% hint style="info" %}
Learn more about direct Vault calls in [how-swapping-works.md](../../how-swapping-works.md "mention")
{% endhint %}

## executeSwap()

Initiates a swap for the given parameters. For ERC-20 tokens, this method will request approval to use the tokens when the allowance is lower than the transfer amount.

```typescript
executeSwap(
  params: ExecuteSwapParams,
  options: ExecuteOptions
): Promise<ContractReceipt>
```

The `ExecuteSwapParams` has the following format:

{% code fullWidth="false" %}
```typescript
type ExecuteSwapParams =
  | { amount: string | bigint; srcChain: "Ethereum"; srcAsset: "ETH"; destChain: "Ethereum"; destAddress: `0x${string}`; destAsset: "FLIP" | "USDC"; }
  | { amount: string | bigint; srcChain: "Ethereum"; srcAsset: "ETH"; destChain: "Polkadot"; destAddress: string; destAsset: "DOT"; }
  | { amount: string | bigint; srcChain: "Ethereum"; srcAsset: "ETH"; destChain: "Bitcoin"; destAddress: string; destAsset: "BTC"; }
  | { amount: string | bigint; srcChain: "Ethereum"; srcAsset: "FLIP"; destChain: "Ethereum"; destAddress: `0x${string}`; destAsset: "USDC" | "ETH"; }
  | { amount: string | bigint; srcChain: "Ethereum"; srcAsset: "USDC"; destChain: "Ethereum"; destAddress: `0x${string}`; destAsset: "FLIP" | "ETH"; }
  | { amount: string | bigint; srcChain: "Ethereum"; srcAsset: "FLIP" | "USDC"; destChain: "Polkadot"; destAddress: string; destAsset: "DOT"; }
  | { amount: string | bigint; srcChain: "Ethereum"; srcAsset: "FLIP" | "USDC"; destChain: "Bitcoin"; destAddress: string; destAsset: "BTC"; }
```
{% endcode %}

The `amount` param will always be in the **base unit of the source asset**, i.e. for `ETH` it will be **Wei**. If no `srcAsset` is provided, the source asset is native `ETH`.

The `ExecuteOptions`  takes a `signer`  and optional `Overrides`

```typescript
export type Overrides = Omit<
  ethers.providers.TransactionRequest,
  'to' | 'data' | 'value' | 'from'
>;
```

### Example

```typescript
import { Chains, Assets } from '@chainflip-io/chainflip-sdk/swap';

// Swaps 1 Ether for Bitcoin
const transactionHash = await swapSDK.executeSwap({ // Identifies the smart contract call to the Vault
  amount: (1e18).toString(), // 1 Ether
  destChain: Chains.Bitcoin,
  destAsset: Assets.BTC,
  destAddress: '1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa',
});
```

The resulting `transactionHash` can be used in [get-status.md](../get-status.md "mention").

## executeSwap() with Cross-Chain Message (CCM)&#x20;

Additionally, the Chainflip vault smart contract has a `call()` function that allows passing an additional \`message\` property to the destination chain. This enables to call a smart contract on the destination chain.

{% hint style="success" %}
This enhanced composability allows any protocol to seamlessly integrate with Chainflip.
{% endhint %}

Initiates a swap for the given parameters and calls the destination address with the given `message`.&#x20;

{% hint style="warning" %}
For ERC-20 tokens, **make sure the token allowance is enough** to cover the transfer amount.
{% endhint %}

The `executeSwap` method can call this smart contract function when passed a  `ccmMetadata` object with the rest of the `ExecuteSwapParams`

{% code fullWidth="false" %}
```typescript
type CcmMetadata = {
  message: string;
  gasBudget: string;
}

type ExecuteSwapParams =
  | { amount: string | bigint; srcChain: "Ethereum"; srcAsset: "ETH"; destChain: "Ethereum"; destAddress: `0x${string}`; destAsset: "FLIP" | "USDC"; ccmMetadata: CcmMetadata; }
  | { amount: string | bigint; srcChain: "Ethereum"; srcAsset: "FLIP"; destChain: "Ethereum"; destAddress: `0x${string}`; destAsset: "USDC" | "ETH"; ccmMetadata: CcmMetadata }
  | { amount: string | bigint; srcChain: "Ethereum"; srcAsset: "USDC"; destChain: "Ethereum"; destAddress: `0x${string}`; destAsset: "FLIP" | "ETH"; ccmMetadata: CcmMetadata; }
```
{% endcode %}

The `amount` and `gasBudget` will always be in the **base unit of the source asset**, i.e. for `ETH` it will be **Wei**.&#x20;

{% hint style="warning" %}
The `message` param is a HEX-encoded sequence of bytes that should be passed with the swapped assets.
{% endhint %}

### Message Receiver

Anyone looking to integrate Chainflip **must implement the following Solidity function signature**   on the destination address:

```solidity
function cfReceive(
    uint32 srcChain,
    bytes calldata srcAddress,
    bytes calldata message,
    address token,
    uint256 amount
) external payable;
```

{% hint style="warning" %}
If the destination address is not a contract or the contract call fails, the Chainflip protocol won't submit the transaction to the destination chain.&#x20;

**This can lead to a loss of funds.**
{% endhint %}

### Example

This is how the `executeSwap()` function looks like with a cross chain message :

```typescript
import { Chains, Assets } from '@chainflip-io/chainflip-sdk/swap';

// Swaps 1000 USDC for ETH and pass a message
const transactionHash = await sdk.executeSwap({
  amount: (1000e6).toString(), // 1000 USDC
  destChain: Chains.Ethereum,
  destAsset: Assets.ETH,
  srcAsset: Assets.USDC,
  destAddress: '0x1234567890abcdef1234567890abcdef12345678',
  ccmMetadata: {
    message: '0xdeadc0de',
    gasBudget: (1e6).toString(), // 1 USDC will be swapped for ETH to pay for gas
  }
});
```

The resulting `transactionHash` can be used in [get-status.md](../get-status.md "mention").
