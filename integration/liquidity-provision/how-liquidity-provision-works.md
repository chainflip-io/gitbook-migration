# How Liquidity Provision Works

Users providing liquidity in the Chainflip JIT AMM follow a process similar to a centralized exchange flow.

1. **Creating a Liquidity Provider Account**: Liquidity providers need an on-chain account for the Chainflip State Chain and refund addresses for each asset (just like [running-a-broker](../swapping-and-aggregation/running-a-broker/ "mention")). They use the **State Chain Gateway** and fund their account with a nominal amount of $FLIP to cover gas fees.&#x20;
2. **Depositing Assets to a Liquidity Account**: Liquidity Providers nominate the assets they wish to deposit, including asset type and refund address. They then send funds to each **Deposit Channel Address** using any wallet, and the transactions are witnessed by validators to credit balances on the State Chain. The Deposit Channel closes after 24 hours, and users should open a new one for each deposit.
3. **Managing Liquidity Positions**: To create a range order or limit order in the JIT AMM, Liquidity Providers must deposit enough assets:
   * Range orders can be opened and closed through simple extrinsics, with parameters similar to Uniswap v3.&#x20;
   * Limit orders are maker-only, requiring a normal swap, and filled orders are automatically closed with swapped funds returned as a free virtual balance.
4. **Withdrawing Collateral from a Liquidity Account**: When Liquidity Providers request asset withdrawals to their specified refund address, the Authority Set creates a valid transaction, deducts gas fees, and sends the funds to the Liquidity Provider on the relevant external chain. The Authority Set witnesses the egress transaction and records it as settled.

{% hint style="info" %}
Check [Liquidity Provider Accounts](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/liquidity-provider-accounts "mention") to learn more
{% endhint %}
