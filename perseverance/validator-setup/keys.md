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

Validator nodes take actions on behalf of the Chainflip network, and all of these actions are authorised using cryptographic keys. Specifically, to fun a validator node, you will need two private/public key pairs: an Ethereum key pair for interacting with Ethereum, and a Chainflip-native  key pair for interacting with the State Chain.

### Generating Validator Keys

It is recommended to generate and save the required keys using the following `chainflip-cli` command:

```bash
chainflip-cli generate-keys --path /etc/chainflip/keys
```

This should give you output similar to the below:

```
Generated fresh Validator keys for your Chainflip Node!

🔑 Node Public Key: 0x7f6763986789c8f4c050e164ac07713d0efffdfb6c74e03a423efa2611324d46
🔑 Ethereum Public Key: 0x03a1fa5c76e0e2f827a32c9241ea44506003818b20ac2c02280beacb07e04dc247
👤 Ethereum Address: 0x0x9915f07018c55e53c326e0ce4fd0c2c7879b9e4c
🔑 Validator Public Key: 0xb0a6578a000c2def9f968d3869ddc905b63e0af8bed37f736f4a19e55ecad562
👤 Validator Account ID: cFMs6fq1KTjBSSC4sn5XZLRyRpjMzLYfUymHALC19kDaDgRmd

🌱 Seed Phrase: spy peanut bless renew property gossip exhibit access claim metal swap sample


❗️❗️
❗️ THIS SEED PHRASE ALLOWS YOU TO RECOVER YOUR CHAINFLIP ACCOUNT KEYS AND ETHEREUM KEYS.
❗️ HOWEVER, THIS SEED PHRASE SHOULD ONLY BE USED IN CONJUNCTION WITH THIS UTILITY. NOTABLY,
❗️ IT CANNOT BE USED TO IMPORT YOUR ETHEREUM ADDRESS INTO METAMASK OR ANY OTHER WALLET IMPLEMENTATION.
❗️ THIS IS BY DESIGN: THIS ETHEREUM KEY SHOULD BE USED EXCLUSIVELY BY YOUR CHAINFLIP NODE.
❗️❗️


💾 Saved all secret keys to '/etc/chainflip/keys'.
```

**Make sure to back up your Seed Phrase and make a note of the public keys and account ID. You will need the Seed Phrase if you ever need to restore your node or recover your funds if you lose access to the node. DO NOT LOSE THIS.**

*If the above command returned any errors, please ensure that the provided --path is accessible and that there are not already keys stored there! For security reasons, the files will not be overwritten automatically, so if you want to replace the stored keys, please make sure to move the old ones first, and back them up somewhere.*

Take special note of the Validator Account ID beginning with `cF`. This is the ID that you will need to add funds and track your node.

{% hint style="danger" %}
**NEVER REVEAL YOUR PRIVATE KEYS TO ANYONE.**
{% endhint %}

### Setting Up Your Ethereum Account

You must ensure that the public Ethereum address generated above has at least 0.1 gETH. **Make sure you send 0.1 gETH to this account's address before trying to add funds to your validator node.** This requirement is subject to change based on Goerli Ethereum transaction fees but for now should be sufficient.

Please also note that the Ethereum key is for the exclusive use of the Validator node. **Do not re-use this key elsewhere.**

### Final Checks

The *private* keys generated above should have been stored at the path provided to the generate-keys command. As a sanity check, make sure they are where you expect: ls `/etc/chainflip/keys` should show that there are three freshly created files: `ethereum_key_file`, `node_key_file`, `signing_key_file`.

### Recovering Your Keys

In case you lose access to your node, you can recover the private keys using the seed phrase. Using the example phrase generated above:

```bash
chainflip-cli generate-keys \
    --path /etc/chainflip/keys \
    --seed-phrase 'spy peanut bless renew property gossip exhibit access claim metal swap sample'
```

The output should be the same as above, and the keys should be written to the provided path, as above.

### Cleaning Up After Yourself

The following commands will ensure that only the current user can read the files, and that the private keys are not available in your shell history:

```bash
sudo chmod 600 /etc/chainflip/keys/ethereum_key_file
sudo chmod 600 /etc/chainflip/keys/signing_key_file
sudo chmod 600 /etc/chainflip/keys/node_key_file
history -c
```
