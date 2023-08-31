# Swapping Basics

Chainflip swaps are _fire-and-forget_, meaning once a deposit is made, users don't need to do anything else for the swap to take place.&#x20;

In a nutshell, the swapping process starts when a deposit is made to the _State Chain_ through a process called [Ingress](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/validator-network/ingress-witnessing-deposits).

The [State Chain](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/validator-network/the-state-chain) is an application-specific blockchain designed for the Chainflip protocol, making cross-chain swap transactions efficient. Whenever funds are deposited, the State Chain witness the deposit, where they are bundled and processed to be swapped in the [Just-In-Time AMM](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/just-in-time-amm-protocol) protocol — all in the same block.

Once the funds are swapped, the new assets are ready to be sent to the destination address by a process called [Egress](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/validator-network/egress-broadcasting-funds).

{% hint style="info" %}
Learn more in [Native Swap Flow](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/native-swap-flow "mention")
{% endhint %}

## Example: USDC (Ethereum) -> BTC (Bitcoin)

Essentially, the process of JIT swaps on Chainflip looks like this for a user:

1. A user initiates a swap by depositing **10,000 USDC** **to buy** **BTC**. The deposit is made through a swap [Deposit Channel](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/deposit-channels-and-brokers) linked to their BTC address.
2. Market Makers monitor the Ethereum blockchain for the deposit and anticipate its execution on the Chainflip State Chain.
3. **After 4 Ethereum blocks**, the deposit transaction is considered **final in the State Chain**, and is expected to be executed in about 48 seconds (8 Chainflip State Chain blocks).&#x20;
4. Market Makers now **must include any limit or range order updates** to compete for the trade on the **BTC-USDC pool**. They use risk model calculations and their capital on other exchanges to determine their best possible price for the trade.
5. The swap deposit reaches the witness threshold and is executed. The swap consumes some range orders before the pool price matches the next best limit order.
6. The Chainflip Validator network sends the swapped BTC funds to the user's native BTC wallet address.

{% hint style="info" %}
Learn more in [Just In Time AMM Protocol](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/just-in-time-amm-protocol "mention")
{% endhint %}

## Example: BTC (Bitcoin) -> ETH (Ethereum)

The process is almost identical to the previous example, but in this case, there are **two pools involved**:

1. A user initiates a swap by depositing **1 BTC** **to buy** **ETH**. The deposit is made through a swap [Deposit Channel](javascript-sdk/swap-assets/request-deposit-address.md#requestdepositaddress) linked to their ETH address.
2. Market Makers monitor the Bitcoin blockchain for the deposit and anticipate its execution on the Chainflip State Chain.&#x20;
3. **After 3 Bitcoin blocks** (\~30 minutes), the deposit transaction is considered **final in the State Chain**, it is expected to be executed in about 48 seconds (8 Chainflip State Chain blocks).
4. Market Makers now **must include any limit or range order updates** to compete for the trade on the **BTC-USDC and ETH-USDC pools**. They use risk model calculations and their capital on other exchanges to determine their best possible price for the trade.
5. When a swap route requires multiple swaps through pools, **each swap is conducted sequentially, usually in the same block**.
6. The swap deposit reaches the witness threshold and is executed. Each swap consumes some range orders before the pool price matches the next best limit order.
7. The Chainflip Validator network sends the swapped ETH funds to the user's ETH wallet address at the end.

It's important to note how Chainflip uses USD as the default pairing for all liquidity pools, with USDC (ERC20) as collateral. This design choice optimizes pricing while reducing liquidity fragmentation and aggregates slippage.

### Using a USDC as an intermediate asset

USDC is chosen as the primary stablecoin base asset due to its high liquidity, adoption in DeFi, and secure structure compared to other options like USDT and algorithmic-stablecoins.

{% hint style="info" %}
Learn more in [$USDC Denominated Pools](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/usdusdc-denominated-pools "mention")
{% endhint %}
