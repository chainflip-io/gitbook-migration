---
description: But first,
---

# Prerequisites

## Ethereum Accounts

We highly advise **against** using a digital wallet that has any real funds in this setup. For the purposes of joining Chainflip's or any other testnet, it is a best-practice to create new wallets.

You'll need two Ethereum Accounts:  the first one (we will refer to it as _**your**_ _**Wallet/Account**_) will be used for receiving `tFLIP` and Interacting with the Auctions App to add and redeem funds and the second one (we will refer to it as the _**Validator Wallet/Account**_) will be used by the validator to send transactions to the Goerli Ethereum blockchain.

## gETH (Goerli Ether)

Each Validator is also responsible for submitting transactions to the Ethereum blockchain. Since Ethereum is account-based, you are required to supply your own `gETH` in order to pay the up-front cost for the transactions.

**Chainflip recommends that you have **_**at least**_** 0.1 gETH in your Validators' account at all times**. You must ensure that this gETH is loaded onto the Validators' wallet _before_ you try to add funds.\
You'll also need to have gETH in your wallet to cover the gas fees for Adding and Redeeming Funds operations.

{% hint style="info" %}
**gETH is Goerli Ether and can be obtained from a Goerli faucet. Faucets are not always reliable, so if you have an issue obtaining gETH, please message the Discord**\
[`🙋︱help-and-support`](https://discord.com/channels/824147014140952596/824147718444023848) **channel.**

**Faucets:**

* [Paradigm Multifaucet](https://faucet.paradigm.xyz)
* [Goerli PoW Faucet](https://goerli-faucet.pk910.de/)
{% endhint %}

{% hint style="info" %}
If Goerli Ethereum Testnet is not showing on your Wallet, make sure to enable Testnets in your wallet settings.
{% endhint %}

You will need a way for your validator to monitor the Goerli Ethereum Blockchain. The Chainflip Engine software allows you to use any endpoint you specify in its config file. You can either [run a geth client](https://github.com/ethereum/go-ethereum) (make sure to set this up on the Goerli testnet!), or you can rely on a third-party remote client provider such as [Alchemy](https://alchemy.com/), [Infura](https://infura.io/), or [Rivet](https://rivet.cloud/).

{% hint style="warning" %}
Please note, gas cost reimbursements are currently not implemented for Validators. Validators will need to ensure their wallets are topped up with gETH or they may run out and be unable to broadcast on behalf of the network. This is a limitation of the Vault Contract design, however, automatic reimbursement is on the future feature list.
{% endhint %}

## System Requirements

Chainflip Validator machines are required to perform some computationally expensive tasks. A cheap VPS **will not** [cut the mustard](https://idioms.thefreedictionary.com/cut+the+mustard). We want to see how the network performs using a diverse set of machines, so we will not be offering too much in the way of suggestions in these docs, but Chainflip recommends a **minimum** of:

```
OS: Ubuntu 22.04 / 20.04 | Our binaries are not compiled on any other OS
CPU: 4GHz+ amd64/x86_64 CPU Architecture | 4+ Dedicated Cores or 6+ Shared Cores
RAM: 8GB+
SSD: 50GB+ NVMe (this may increase over time)
Bandwidth: Minimum 400MBps, Recommended 1GBps connection, 100 GB bandwidth combined up/down per month
Provider: Any execpt Contabo. DO NOT USE CONTABO.
```

This machine must be available 24/7 and thus it is recommended that you only try this on machines with a stable and reliable internet connection, such as a VPS or Dedicated Server. If you would like some help picking a server to rent, you can come  to the Chainflip Community Discord and ask for help in the [`🛠︱technical-discussion`](https://discord.com/channels/824147014140952596/824147608377098241) or the [`💬-node-support`](https://discord.com/channels/824147014140952596/1096065647361925161) channels.

### Firewall

You should ensure that all connections on ports `30333 (TCP)` and  `8078 (TCP)` are not blocked! Some VPS providers enable an all-port firewall by default, but if this is not the case your node will not be able to participate in Keygen / Signing ceremonies and as a result will never become active.
