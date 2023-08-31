---
description: We told you to make a copy, but you insisted not to 🤷‍♂️
---

# Recover Private Keys

{% hint style="danger" %}
⛔️ All the information you get back when running any of the commands below is private sensitive data. **Do not share it with anyone. ⛔️**

<mark style="color:red;">**Whoever has access to these keys can take all your tFLIP added to your validator node.**</mark>
{% endhint %}

You should always have a copy of all your keys somewhere safe outside the Server on which the node is running.

If for whatever reason you lost your keys but still have access to your server, don't freak out (yet).

You can run some magical commands to get the keys.

### Getting Node ID

The following command will return the Node ID of your validator node:

```bash
sudo chainflip-node key inspect-node-key --file /etc/chainflip/keys/node_key_file
```

### Getting The Secret Seed&#x20;

This command will return the private key of your node and other keys. <mark style="color:red;">**DO NOT share this with anyone.**</mark>

Chainflip <mark style="color:red;">**will never**</mark> ask you to reveal these keys under any circumstances.

To get your Secret Seed that was used to generate your signing key, run the following command:

```bash
chainflip-node key inspect "0x$(sudo cat /etc/chainflip/keys/signing_key_file)"
```

The output will look like this:

```bash
Secret Key URI `0x` is account:
  Network ID:        chainflip 
  Secret seed:       <YOUR SECRET SEED> # <-- Don't shate it with anyone
  Public key (hex):  0x1803aecb4e11790e73f775206836f25b4348a3290a190319b4b075d9ccbd6349
  Account ID:        0x1803aecb4e11790e73f775206836f25b4348a3290a190319b4b075d9ccbd6349
  Public key (SS58): cFJQy58CJKJhNCBnV89qQhcQYQSgC6cg8dGWiTJb8xqWsMyQ3
  SS58 Address:      cFJQy58CJKJhNCBnV89qQhcQYQSgC6cg8dGWiTJb8xqWsMyQ3
```

