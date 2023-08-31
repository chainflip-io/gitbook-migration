# Liquidity Provision Basics

In the context of Chainflip, liquidity provision is the process of supplying assets to the Chainflip protocol's liquidity pools. By providing liquidity, you're essentially lending your assets to the protocol to be used in these swaps.

Liquidity provision is a crucial aspect of the Chainflip protocol. It ensures that there are sufficient assets in the system for swaps to occur smoothly and efficiently.

{% hint style="info" %}
Learn more in [Liquidity Provider Accounts](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/liquidity-provider-accounts "mention")
{% endhint %}

### How to Become a Liquidity Provider

Liquidity providers (LPs) are entities that supply assets to Chainflip's liquidity pools. In return for their contribution, LPs earn fees. The more liquidity an LP provides, the more fees it can potentially earn.

LPs can create **range orders** or **limit orders** in the JIT AMM. To do so, they need to have deposited enough assets to open the position from their virtual balance on the State Chain.

Range orders can be open on any given pair (pool) and are similar to Uniswap v3. These can be closed or updated at any time.

Limit orders are maker-only. Filled limit orders are automatically closed and the swapped funds are returned to the LP account balance. Unfilled orders can be cancelled at any time.

### Risks and Rewards

While providing liquidity can be a profitable endeavour due to the fees earned from swaps, it's not without risks. One of the main risks is known as **Impermanent Loss (IL)**, which can occur when the price of the assets in the pool changes significantly, leading to less profitability versus holding the asset.

However, Chainflip employs strategies to mitigate these risks and ensure that LPs are adequately compensated for their contribution. The protocol JIT AMM (Just-in-Time) ....
