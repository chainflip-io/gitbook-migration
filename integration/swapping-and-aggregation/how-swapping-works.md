# How Swapping Works

At a high level, the process can be described with the following steps**:**

1. **Initiating a swap:** This first step can be done through the [Swapping app](https://swap-partnernet.chainflip.io/) (front end), the [Javascript SDK](javascript-sdk/), or directly using the chainflip CLI client. There are two primary methods to start a Chainflip Swap:
   * **Generating a Deposit Address**: This method involves creating a unique deposit address where the user can send the funds. Once the deposit is made, the swap process is automatically triggered. See [Request Deposit Address](javascript-sdk/swap-assets/request-deposit-address.md) method.
   * **Direct call to the Vault Smart Contract (EVM chains only):** This method involves calling the `swap()` function in Chainflip Vault contracts. It offers a higher degree of control, making it suitable for advanced users (like DEX Aggregators). See [Execute Swap / Call](javascript-sdk/swap-assets/execute-swap.md) method.
2. **Witnessing the deposit:** The Validator network will monitor open [Deposit Channels](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/deposit-channels-and-brokers) for incoming transactions and Vault Contracts for swap calls. Upon seeing a deposit for a Swap, it will be witnessed and registered on the State Chain.

{% hint style="warning" %}
There's a delay between the deposit transaction and its confirmation on the State Chain. On Ethereum, is 6 blocks (\~90 seconds), whereas Bitcoin is 3 blocks (\~30 minutes).
{% endhint %}

{% hint style="success" %}
At this point, you can use the [Block Explorer](https://blocks-partnernet.chainflip.io/swaps) to see the swap '_in progress_'.
{% endhint %}

3. **Processing the swap:** Once the swap is Witnessed it is ready to be swapped in the [Just-In-Time AMM](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/just-in-time-amm-protocol) protocol. Swaps are automatically bundled and processed so that LPs can bid on swaps in the same block where they occur.\
   For swap routes that require more than one pool, i.e. BTC -> ETH, each swap is conducted sequentially, BTC -> USDC, USDC -> ETH, and happens all in the same block.

{% hint style="info" %}
The swap input is first denominated in USD. This always occurs at some stage in the swapping process as a function of [$USDC Denominated Pools](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/usdusdc-denominated-pools "mention").
{% endhint %}

4. **Broadcasting the swap output:** Swapped assets on the State Chain are now listed as Pending Egress Transactions before being paid out to the user's destination address. These transactions are processed in a batch with estimated gas fees deducted to compensate the network for the [Broadcast](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/validator-network/egress-broadcasting-funds).
