# Running a Broker

The Chainflip Broker API Bundle allows SDK users to run their own _State Chain_ interface.

A Broker, in the context of the Chainflip protocol, is an entity that facilitates the process of initiating a swap on behalf of end users via [request-deposit-address.md](../javascript-sdk/swap-assets/request-deposit-address.md "mention"). They provide an endpoint to interact with the State Chain, reducing the complexity of transactions and lowering gas fees.

## How Brokers work

1. Any _State Chain_ account can act as a Broker, submitting **Deposit Channel Requests** extrinsics, simplifying the user experience and reducing gas fees.
2. Brokers take swap intent and return the extrinsic hash, once a Deposit Channel on the State Chain is opened for the user. The Deposit Channel can then be verified before a deposit is made.
3. Brokers pay a small transaction fee in $FLIP for each extrinsic they submit, which is then burned. This is to avoid spam prevention and for network integrity reasons.

{% hint style="info" %}
Learn more in [Deposit Channels & Brokers](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/swaps-amm/deposit-channels-and-brokers "mention")
{% endhint %}

Optionally, Brokers can choose to charge a fee for the use of their endpoint, to offset the costs they incur for facilitating transactions.

## Earning Fees as a Broker

Broker operators can choose to charge a fee for the use of their endpoint, and can be set at any value from 1 basis point to 1000 basis points. This fee is taken in USDC immediately after the Network Fee has been deducted.

If you are a developer of wallets, aggregators or DEXes, you can earn revenue by bringing volume to Chainflip's ecosystem and offset the costs of running a Broker.

