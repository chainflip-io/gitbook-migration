---
description: Following the flow of a native Chainflip cross-chain swap
---

# Native Swap Flow

Let's look at how native cross-chain swaps are done from end-to-end in the Chainflip protocol. Chainflip swaps are **fire-and-forget**, meaning once the deposit is made, the user doesn't need to do anything for the swap to take place.

### Initiating a Swap

Either through the frontend (Swapping app) or the Chainflip SDK, there are multiple methods of starting a Chainflip Swap.

* Registering an intent to swap using a [Broker and creating a Deposit Channel](deposit-channels-and-brokers.md)
* Calling the swap function in Chainflip Vault contracts, which can either be done by the user or by other contracts for maximum composability (EVM chains **only**)
* Directly submitting a swap Deposit to a Vault using special transaction metadata (Not yet built)

{% hint style="info" %}
Funds sent straight to Chainflip vaults without initiating it using one of these methods **will result in the loss of funds**, as the network won't know what to do with it.
{% endhint %}

All of the above methods will register the swap and essential information, such as:

* Destination Asset
* Destination Chain
* Destination Address
* Any call data for a follow-on Cross-Chain Message (such as a Uniswap or external Bridge call)

Once a method is chosen, the user can send their funds on the external chain (BTC, Ethereum, to be swapped in accordance with their chosen method.

{% hint style="warning" %}
Deposit Channels remain open for 24 hours. After that time, the network may no longer recognise late deposits. Users should **always open a new Deposit Channel** and send their deposit **immediately** every time they swap.
{% endhint %}

### Witnessing the Deposit

The network will monitor open Deposit Channels for transactions and the Vault Contracts for swap calls. Upon seeing a deposit for a Swap, it will be [Witnessed](native-swap-flow.md#witnessing-the-deposit) by the network and registered on the State Chain.

The delay between the deposit transaction being made on the source chain and its confirmation on the State Chain through Witnessing depends mainly on the source chain. On Ethereum, the threshold is currently 6 blocks (\~90 seconds), whereas Bitcoin is 3 blocks (\~30 minutes).&#x20;

{% hint style="info" %}
Pre-witnessing will allow the network to confirm transactions faster or slower depending on the value of that deposit. This will cut down the confirmation window for small deposits, and increase the security for larger transfers. This feature is currently under active development.
{% endhint %}

### Processing the Swap

Once the swap is Witnessed it is ready to be swapped in the **JIT AMM**.

At the end of each block, Swaps are automatically bundled and processed. This is so that liquidity providers can bid on swaps in the same block where they occur. Liquidity providers monitor incoming deposits to bid on them at [their best price](just-in-time-amm-protocol.md).

* For each asset in each pool, swaps that take place in the same block are bundled together and swapped in one step to eliminate frontrunning and MEV potential. This is done separately for each direction of trade.
* For swap routes that require multiple swaps through pools, for example, BTC -> USDC -> ETH, each swap is conducted sequentially, usually in the same block.
* Whenever the swap input is first denominated in USD, the Chainflip Network fee is deducted. This could be at the start, middle, or end of swap processing depending on the route. This always occurs at some stage in the swapping process as a function of having[usdusdc-denominated-pools.md](usdusdc-denominated-pools.md "mention").
* For each swap conducted, liquidity fees are collected and the output is forwarded to the next step.

Once all required swaps have been completed, the swap output is ready for the final stage of processing.

### Broadcasting the Swap Output&#x20;

Once a swap has been automatically executed on the State Chain, the assets to be paid out to the user are listed as a [**Pending Egress Transaction**](../validator-network/egress-broadcasting-funds.md).&#x20;

Using the Destination Address specified in the user’s Swap Initiation step, and bundling the Pending Egress Transaction with others into a single batch, estimated gas fees are deducted from the final Swap Output amount to compensate the network for making the Broadcast.&#x20;

If the user has included external call data in their swap request, that transaction will be broadcast separately to account for additional gas costs incurred at the user's request that shouldn't be socialised, but the egress is executed using the same TSS and broadcast method.

The final **Egress Payout Transaction** is constructed, and the Authority Set conducts a Signing Ceremony to produce a valid signed transaction for that chain, which is [Broadcast](../validator-network/egress-broadcasting-funds.md)[ed](../validator-network/egress-broadcasting-funds.md) automatically by the network.

The user now has their shiny new native assets sitting peacefully in their wallet (or anywhere else that they specified).

### Swap Fees In Summary

<table><thead><tr><th width="194.33333333333331">Fee Type</th><th width="171">Fee Level</th><th>Description</th></tr></thead><tbody><tr><td>Deposit Gas Fees (Normal User Transfer)</td><td>Varies per chain and deposit type, but usually 31k gas on Ethereum</td><td>Network gas fees will vary depending on the incoming chain. Coming from BTC will differ from those coming from ETH for example. These will be paid by the swapper when they deposit from their wallet.</td></tr><tr><td>Liquidity Fee Per Pool</td><td>0.05% - 0.20%</td><td>Fixed Liquidity Provider Fee (Pool Defined)</td></tr><tr><td>Network Fee for Buy/Burn Mechanism</td><td>0.10%</td><td>Fixed Network Fee used by the protocol to buy FLIP tokens from the USDC/FLIP pool on the State Chain, which in turn are burnt.</td></tr><tr><td>Destination Chain Transfer Broadcast Fee (Transfer by Protocol)</td><td>Varies per chain</td><td>Network gas fees will vary depending on the withdrawal chain. Batching, however, will lower the fee that would normally be paid.</td></tr></tbody></table>

