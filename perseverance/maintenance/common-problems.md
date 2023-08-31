---
description: Why am I still offline?!
---

# Common Problems

Once we know what the common problems are, we'll update this page so you can easily troubleshoot your node. Until then, head to the next page to help us figure out what's inevitably going wrong!

## How does this page work?

This page is designed to help you fix all of your problems on your own!&#x20;

First of all you need dump recent logs from your terminal.

```bash
journalctl -f -u "chainflip-*"
```

In order to quickly understand your issue, please first check if your log output resembles any of these.

## Not Funded

You will see this log before the Chainflip network witnesses that you added funds to your validator. Once you have successfully added funds, it can take a while for the network to register it. Your node might appear <mark style="color:red;">`Offline`</mark> until then.

#### Logs

{% code overflow="wrap" %}
```json
{"tag":"","msg":"Your Chainflip account cFNugEinCy5Zxv44B4o8uU4PSigKmmzdUYabbZbfNjAAZx18a is not funded. WAITING for account to be funded at block: 143222","level":"warn","ts":"2022-11-18T09:45:37.474214013+00:00","component":"StateChainConnector"}

```
{% endcode %}

#### Solution

Please make sure you followed all the steps outlined in [funding.md](../funding/funding.md "mention").

If after **30 minutes** your engine is still not online, try restart your engine\
`sudo systemctl restart chainflip-engine`.

## **Incorrectly configured the ETH RPC**

This can have a number of possible issues.&#x20;

#### Logs

{% code overflow="wrap" %}
```json
{"tag":"","msg":"Connecting new HTTP web3 client to https://eth****","level":"debug","ts":"2022-11-18T09:25:21.576142234+00:00","component":"Eth-DualRpcClient"}
Error: Failed to create EthDualRpcClient

Caused by:
    Inconsistent chain configuration. Terminating.Expected ETH chain id 5, received 1 through WebSocket., Expected ETH chain id 5, received 1 through HTTP.
```
{% endcode %}

#### Solution

You have not selected the correct Ethereum network. In this case, you have selected `mainnet`. The Chainflip network uses Görli. Please reread [#pointing-to-the-ethereum-client](../validator-setup/engine-settings.md#pointing-to-the-ethereum-client "mention").

#### Logs

{% code overflow="wrap" %}
```json
{"tag":"","msg":"Connecting new WebSocket web3 client to wss://eth****","level":"debug","ts":"2022-11-18T10:09:02.643481895+00:00","component":"Eth-DualRpcClient"}
Error: Failed to create EthDualRpcClient

Caused by:
    0: Failed to create EthWsRpcClient
    1: Error: 'web3::transports::WebSocket::new(&eth_settings.ws_node_endpoint).await' with type 'core::result::Result<web3::transports::ws::WebSocket, web3::error::Error>' failed at https://github.com/chainflip-io/chainflip-backend/tree/5e37992a14bb14d8d0609687ebe8f72e9402fbc0/engine/src/eth/rpc.rs#L266#C5
    2: code 401
```
{% endcode %}

#### Solution

You have entered your RPC endpoint in incorrectly. Please make sure you copy it over without any mistakes.

#### Logs

{% code overflow="wrap" %}
```json
{"tag":"","msg":"Connecting new WebSocket web3 client to wss://SOM****.goerli.ws.rivet.cloud","level":"debug","ts":"2022-11-18T10:14:58.473000904+00:00","component":"Eth-DualRpcClient"}
Error: Failed to create EthDualRpcClient

Caused by:
    0: Failed to create EthWsRpcClient
    1: Error: 'web3::transports::WebSocket::new(&eth_settings.ws_node_endpoint).await' with type 'core::result::Result<web3::transports::ws::WebSocket, web3::error::Error>' failed at https://github.com/chainflip-io/chainflip-backend/tree/5e37992a14bb14d8d0609687ebe8f72e9402fbc0/engine/src/eth/rpc.rs#L266#C5
    2: Ssl(Error { code: ErrorCode(1), cause: Some(Ssl(ErrorStack([Error { code: 337047686, library: "SSL routines", function: "tls_process_server_certificate", reason: "certificate verify failed", file: "../ssl/statem/statem_clnt.c", line: 1913 }]))) }, X509VerifyResult { code: 62, error: "Hostname mismatch" })
```
{% endcode %}

#### Solution

You have copied the `chainflip-engine` config without changing any of the values! Please re-read the steps in [engine-settings.md](../validator-setup/engine-settings.md "mention"). You need to make that you use the URL from your Ethereum Client. [#pointing-to-the-ethereum-client](../validator-setup/engine-settings.md#pointing-to-the-ethereum-client "mention").

## Auctions App confusion

#### I'm still <mark style="color:red;">Offline</mark>

![](<../.gitbook/assets/Screenshot 2022-11-18 at 11.23.44.png>)

Likely you haven't completed all the steps. Please start from [keys.md](../validator-setup/keys.md "mention")and work your way very slowly, making sure you have read everything.&#x20;

It can be, that you did not successfully submit the `chainflip-cli` commands. Please run all the commands in [#registering-validator-keys](../funding/funding.md#registering-validator-keys "mention").

#### How can I be <mark style="color:green;">Active - Online</mark>

![](<../.gitbook/assets/Screenshot 2022-11-18 at 11.35.43.png>)

You need to wait until the next Rotation. Make sure your node is in the <mark style="color:green;">**Backup - Online**</mark> mode.

<figure><img src="../.gitbook/assets/Screenshot 2022-11-18 at 11.37.13.png" alt=""><figcaption><p>For example, if you're in Backup - Online, you would be included in the next rotation in 57m.</p></figcaption></figure>

#### Why am I <mark style="color:red;">Active - Offline</mark>?

![](<../.gitbook/assets/image (6).png>)

Your engine has stopped submitting Hearbeats. This means either your node, your engine or both have crashed or are misconfigured.

Verify this with:

```
sudo systemctl status chainflip-node
sudo systemctl status chainflip-engine
```

Now check the logs:

```
journalctl -f -u "chainflip-*"
```

## Ethereum Keyfile Error

```
thread 'main' panicked at 'called Result::unwrap() on an Err value: Failed to decode Ethereum Private Key file at /root/chainflip/ethereum_key_file
```

This is due to your key likely either having `0x` on the front, or having a newline at the end. You can use `nano` to edit the file. Make sure to include the `-L` argument, otherwise nano will save the file with a new line at the end! **Make sure that you replace `/path/to/ethereum_key_file` with the correct file path on your system.**

```bash
nano -L /path/to/ethereum_key_file
```

## Your validator wallet is out of gETH

This is a very common problem if you have joined the network early. \
Make sure you have at least **0.1 gETH** at all time in your validator's wallet. Otherwise you'll fail to submit transactions to the Ethereum network.



## Why Slash? Why low reputation?

_Slashoor has paid you a visit, hasn't it?_

### Overview

If you find yourself being penalized and your reputation points dropping, it is possible that the chainflip-engine is not functioning optimally. This discussion will focus specifically on ETH witnessing issues.

One potential cause is using a free-tier managed service for your RPC endpoint provider (such as Alchemy, Rivet, or Infura). This can lead to rate-limiting problems based on network activity and the stability of your engine. Rate-limiting models differ among services. Some have a fixed daily request limit, while others offer unlimited daily requests but impose a cap on "compute units" per second. These varying models may result in different behaviors under certain conditions.

If you encounter rate-limiting, your engine will be unable to witness ETH events, which can lead to immediate penalties or reductions in reputation points, potentially culminating in slashing. Additionally, failure to properly witness ETH events can cause rotations to take longer and reduce your chances of becoming an Authority member.

### Solution

You have multiple options here:

#### Using Two Different ETH RPC Providers

This is a good method for setting up your RPC configuration. Sign up for two accounts, such as [Infura](https://www.infura.io/), [Rivet](https://rivet.cloud/), and [Alchemy](https://www.alchemy.com/) (choose two) and update your engine configuration to use Infura for the `http_node_endpoint` and Rivet for `ws_node_endpoint`, or vice versa. This approach ensures protection against service outages from either provider and reduces the number of daily requests for each service.

#### Subscribe for a paid tier at your RPC Endpoint provider

Subscribe to a paid tier at your RPC endpoint provider: Though this option incurs a monthly fee, it requires minimal effort and is recommended for those who want a hassle-free start without setting up their own ETH nodes or worry about rate-limiting.

#### Run your own ETH node and expose it as an RPC endpoint

This option, which involves connecting directly to the ETH network, is the most reliable solution. However, it demands strong technical skills for setting up and operating ETH nodes, which is beyond the scope of this guide. For more information, visit: [https://geth.ethereum.org/docs/getting-started](https://geth.ethereum.org/docs/getting-started)

### Updating your config file

After setting up your RPC endpoints as described above, you need to update the config file and restart your engine. You can do this by running:&#x20;

```bash
sudo nano /etc/chainflip/config/Settings.toml
```

Your config file will look something like this:

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption><p>Add RPC endpoints to /etc/chainflip/config/Settings.toml</p></figcaption></figure>

{% hint style="warning" %}
Make sure that both endpoints (HTTP and Web Sockets) are accessible. Otherwise your engine will fail to start.
{% endhint %}

Save your changes and exit then restart the engine by running:

```bash
sudo systemctl restart chainflip-engine.service 
```

## It still doesn't work?

Follow instructions under [submitting-an-issue.md](submitting-an-issue.md "mention").

{% hint style="warning" %}
**Please** do this step before raising an issue on Discord. It makes our lives much much easier.
{% endhint %}

