---
description: Don't lose 'em.
---

# Generating Keys

First thing we need is a directory for storing our keys.

```shell
sudo mkdir /etc/chainflip/keys
```

## Ethereum Keys

One of the responsibilities of your Validator is to submit transactions to the Ethereum blockchain. Since Ethereum is account-based, this means every Validator must have their own private keys and be capable of submitting transactions.

You're going to create a new private key, which you can generate easily by creating a [new account in Metamask and exporting the private key](http://help.silamoney.com/en/articles/4254246-how-to-generate-ethereum-keys), but there are dozens of other ways to do this as well… **Keep a note of the address** of the new keypair as you'll need to send gETH to it.&#x20;

{% hint style="danger" %}
**NEVER REVEAL YOUR PRIVATE KEYS TO ANYONE.**
{% endhint %}

It is **highly recommended** that you use a fresh private key which is only used for the purpose of submitting transactions to Goerli for your own Validator. Whilst we can't stop you using the same private key for multiple Validator nodes, know that **Chainflip explicitly recommends against this**. Your Validator is a live server on the internet. We're going to improve the way the software handles key storage. For testnet, do not use a key that has or will have mainnet funds. **Just don't.**

Once you have a private key, you should save it into a file with the following command. Don't forget to replace `YOUR_VALIDATOR_WALLET_PRIVATE_KEY` with your actual private key.

{% hint style="warning" %}
**If there is a 0x on the front, remove it before running the command.**
{% endhint %}

```bash
echo -n "YOUR_VALIDATOR_WALLET_PRIVATE_KEY" |  sudo tee /etc/chainflip/keys/ethereum_key_file
```

**You must ensure that the public address administered by the private key at the above location has at least 0.1 gETH. Make sure you send 0.1 gETH to this account's address before trying to add funds.** This requirement is subject to change based on Goerli Ethereum transaction fees but for now should be sufficient.

## Validator Keys

In order to add funds to the node, you are going to need some Chainflip keys. You can generate them with the `chainflip-node` binary.

### Generating Signing Keys

You can generate keys for Chainflip using the following command:

```bash
chainflip-node key generate
```

It should give you some output like the below:

```
Secret phrase:       XXX
  Network ID:        chainflip
  Secret seed:       0xXXX  # This is your private key. Hold onto it.
  Public key (hex):  0xXXX
  Account ID:        0xXXX 
  Public key (SS58): cFXXX # This is your Validator ID. Make sure you have it handy for adding funds.
  SS58 Address:      cFXXX
```

**Make sure to backup all of these values. You will need them again in order to add funds to your node. If your node ever gets taken down by your hosting provider, you will need these values to stop your funds getting slashed. DO NOT LOSE THEM.**

{% hint style="danger" %}
**NEVER REVEAL YOUR PRIVATE KEYS TO ANYONE.**
{% endhint %}

To finish off the work with the Signing Key, take a special note of the `Public Key (SS58)` of your Signing Key. **That it actually your Validator ID** that you will need to add funds and track your node.

### Loading Your Signing Keys

Take the `Secret seed` from your generated signing keys in the previous step and add it to your validator node using this command, replacing `0x...` with your key.&#x20;

The following command saves the seed into a variable called `SECRET_SEED`:

```bash
SECRET_SEED=0x...
```

And this command saves it to a file called: `signing_key_file` without the first two characters (the `0x`):

```bash
echo -n "${SECRET_SEED:2}" | sudo tee /etc/chainflip/keys/signing_key_file
```

### Generating a Node Key

Finally, we need to do a similar process one more time. We need to generate a separate key that is used for secure communication between Validators. This time run:

```bash
sudo chainflip-node key generate-node-key --file /etc/chainflip/keys/node_key_file
```

You can check that the command worked by running the following command:

```bash
cat /etc/chainflip/keys/node_key_file
```

You should see the contents of the files printed to your terminal.

### Back Them Up & Copy Your Validator ID

Now you should have three separate keys loaded onto your validator. Your **Ethereum Keys**, your **Signing Keys (your main Validator ID)**, and your **Node Key**. In case this wasn't already abundantly clear, you should ensure that you have a copy of **all** of your keys. They cannot be recovered by Chainflip if you are to lose them. If you lose them, you are **almost certainly** going to lose (test) money.

### Cleaning Up After Yourself

The following commands will ensure that only the current user can read the files, and that the private keys are not available in your shell history:

```bash
sudo chmod 600 /etc/chainflip/keys/ethereum_key_file
sudo chmod 600 /etc/chainflip/keys/signing_key_file
sudo chmod 600 /etc/chainflip/keys/node_key_file
history -c
```
