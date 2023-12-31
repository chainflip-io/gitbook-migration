# Javascript SDK

{% hint style="warning" %}
These docs are under active development. 🚧
{% endhint %}

The Chainflip SDK provides a simple and efficient way for developers to interact with the Chainflip protocol, making it easy to build powerful and user-friendly applications.

In this guide we'll explain how to:

1. **Get chains & assets**: Use the `getChains` and `getAssets` methods to retrieve information about the available chains and their respective assets.
2. **Get quotes**: The `getQuote` method provides a quote for a proposed swap. This includes details like the source and destination chains, the tokens involved, and the expected exchange rate and fees.
3. **Swap assets:** There are two main ways to swap assets, depending on the source chain and/or the specific use case:
   1. **Request a deposit address:** The `requestDepositAddress` method provides a deposit address where the user can send the source tokens to be swapped. Funds need to be sent to initiate the swap. The swapped tokens will be sent to the destination address provided.
   2. **Execute swap call:** Alternatively, the `executeSwap` method triggers the swap by calling the `swap()` function in the Chainflip Vault directly (EVM only for now). The swapped tokens will be sent to the destination address provided.
4. **Get status:** The `getStatus` method provides real-time updates about the status of a swap operation.

{% hint style="info" %}
If you want to learn more, check the [Native Swap Flow](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/native-swap-flow "mention") documentation
{% endhint %}

### Support

If you are experiencing issues with the SDK, please go to the #chainflip-sdk channel in Discord for assistance. Not in Discord yet? [Join us](https://discord.gg/chainflip-community).
