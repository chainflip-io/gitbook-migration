---
description: You wouldn't, would you?
---

# Redeeming Funds & Retiring

You can redeem your precious $tFLIP, subject to certain conditions. In order to do so, the funds may neither be bonded, nor tied up in auction bidding.&#x20;

For a detailed account of the process and its restrictions see [Redeeming ERC-20 FLIP through the Gateway](http://127.0.0.1:5000/s/DQUC2wQf4NIhgIHFhCFR/validator-network/state-chain-flip-gateway#redeeming-erc-20-flip-through-the-gateway "mention").

### Retiring from Auctions

To end your participation in auctions, use the Chainflip CLI.

```bash
sudo chainflip-cli --config-root /etc/chainflip stop-bidding
```

Your node will cease to participate in auctions.  You will have all remaining $tFLIP credited to your balance upon conclusion of the final auction. You will still need to follow the next steps to claim them.

### Redeeming your tFLIP

The redemption process can be initiated via the [Chainflip Auctions App](https://auctions-perseverance.chainflip.io/auctions) > My Nodes, and the Chainflip CLI. You will need the address of the Ethereum account to which you want your $tFLIP to be sent.

{% hint style="warning" %}
As of version 0.8, the network levies a Redemption Tax of (at the time of writing) 5 $tFLIP per redemption. This is to reduce 'redemption request spam'. These requests are expensive for the network to process, and the cost for this is now carried by the requester, via the tax.
{% endhint %}

In the Auctions App, select "Redeem". This will guide you through the process.&#x20;

You have two options. By default, the command will redeem the maximum available funds from your account, taking into account the bond. For example, imagine your account balance is 1000 $tFLIP and the bond is 800 $tFLIP, the following would withdraw 795 $tFLIP net of tax, to the Ethereum account `0xcf..cf`:&#x20;

```bash
sudo chainflip-cli redeem 0xcfcfcfcfcfcfcfcfcfcfcfcfcfcfcfcfcfcfcfcf
```

Alternatively, you can specify an exact amount you wish to redeem, say 50 $tFLIP:

```bash
sudo chainflip-cli redeem --exact 50 0xcfcfcfcfcfcfcfcfcfcfcfcfcfcfcfcfcfcfcfcf
```

This will generate a redeem certificate that will be sent to the Ethereum network.&#x20;

Once you have completed the request, proceed to the Auctions App. The redemption should automagically appear here, where you will be able to continue with the process.
