---
description: You used Contabo didn't you...
---

# Migrating to a Different Server

No problem, one of your amazing community members has developed this guide. After you have created a new machine and finished all the steps up to and including [creating-new-linux-user.md](../validator-setup/creating-new-linux-user.md "mention"), follow this guide.

**On Your New Machine:**&#x20;

**Download Binaries via APT Repo**

```bash
sudo mkdir -p /etc/apt/keyrings 
curl -fsSL repo.chainflip.io/keys/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/chainflip.gpg
```

**Verify the key's authenticity:**

```bash
gpg --show-keys /etc/apt/keyrings/chainflip.gpg
```

**After that, add Chainflip's Repo to apt sources list:**

```bash
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/chainflip.gpg] https://repo.chainflip.io/perseverance/$(lsb_release -c -s) $(lsb_release -c -s) main" | sudo tee /etc/apt/sources.list.d/chainflip.list
```

**Installing The Packages**

```bash
sudo apt-get update 
sudo apt-get install -y chainflip-cli chainflip-node chainflip-engine
```

**Adding your existing keys**

```bash
sudo mkdir /etc/chainflip/keys
```

{% hint style="info" %}
**Note:** After this you don't need to generate new Signing Keys, you can skip that phase and continue to the command below with your **old** Seed Secret.
{% endhint %}

**Loading Your Signing Keys**

`YOUR_VALIDATOR_WALLET_PRIVATE_KEY` << Change to Your private key from Metamask.

```bash
echo -n "YOUR_VALIDATOR_WALLET_PRIVATE_KEY" | sudo tee /etc/chainflip/keys/ethereum_key_file
```

`YOUR_CHAINFLIP_SECRET_SEED` << change this text to the **old** Secret Seed

```bash
export SECRET_SEED=YOUR_CHAINFLIP_SECRET_SEED
```

```bash
echo -n "${SECRET_SEED:2}" | sudo tee /etc/chainflip/keys/signing_key_file
```

#### Generating a Node Key

Finally, we need to do a similar process one more time. We need to generate a separate key that is used for secure communication between Validators. This time run:

```bash
sudo chainflip-node key generate-node-key --file /etc/chainflip/keys/node_key_file
```

You can check that the command worked by running the following command:

```bash
cat /etc/chainflip/keys/node_key_file
```

You should see the contents of the files printed to your terminal.

**Back Them Up & Copy Your Validator ID**

```bash
sudo chmod 600 /etc/chainflip/keys/ethereum_key_file 
sudo chmod 600 /etc/chainflip/keys/signing_key_file 
sudo chmod 600 /etc/chainflip/keys/node_key_file 
history -c
```

{% hint style="warning" %}
Make sure to update your config file with the IP address of the new VPS. Otherwise you'll get slashed once you start the engine on the new VPS.
{% endhint %}

{% hint style="danger" %}
:bomb: **WARNING** :atom: Do not run two instances of your Validator at the same time. You will almost certainly be slashed. Make sure you turn off your old server before you turn on your new one.
{% endhint %}

After that make sure to stop the engine and the node on the old VPS by running:

**On the **<mark style="color:red;">**Old**</mark>** VPS**

```bash
sudo systemctl stop chainflip-node.service
sudo systemctl stop chainflip-engine.service
sudo systemctl disable chainflip-node.service
sudo systemctl disable chainflip-engine.service
```

**On the **<mark style="color:green;">**New**</mark>** VPS**

Setup the config file as explained here:

{% content-ref url="../validator-setup/engine-settings.md" %}
[engine-settings.md](../validator-setup/engine-settings.md)
{% endcontent-ref %}

Then start the node and engine services as explained here:&#x20;

{% content-ref url="../validator-setup/installation-pt.-2.md" %}
[installation-pt.-2.md](../validator-setup/installation-pt.-2.md)
{% endcontent-ref %}
