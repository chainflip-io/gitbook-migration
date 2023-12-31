---
description: Tell computer what do.
---

# Engine Settings

We need to create the engine config file.

```shell
sudo mkdir -p /etc/chainflip/config
sudo nano /etc/chainflip/config/Settings.toml
```

### Editing the Config

Copy the following to your `nano` editor. You also need to replace `IP_ADDRESS_OF_YOUR_NODE` with the public IP Address of your server. To get the public IP of your node you can run this command: `curl -w "\n" ifconfig.me`.&#x20;

Also you'll need to provide the `ws_node_endpoint`, and `http_node_endpoint` for whichever [Ethereum client](engine-settings.md#pointing-to-the-ethereum-client) you've selected. It will look different depending on which client you select:

* Rivet - `wss://SOME_LONG_SECRET_INFORMATION.goerli.ws.rivet.cloud`
* Infura - `wss://goerli.infura.io/ws/v3/SOME_LONG_SECRET_INFORMATION`
* Alchemy - `wss://eth-goerli.g.alchemy.com/v2/SOME_LONG_SECRET_INFORMATION`

{% hint style="info" %}
For optimal performance and better resilience against RPC endpoint provider outages and rate limiting, some operators use one service for the `http_node_endpoint` and another provider for the `ws_node_endpoint.` This has had mixed results, you can try it if you wish.

Make sure that both endpoints (HTTP and Web Sockets) are accessible. Otherwise your engine will fail to start.
{% endhint %}

```toml
# Default configurations for the CFE
[node_p2p]
node_key_file = "/etc/chainflip/keys/node_key_file"
ip_address = "IP_ADDRESS_OF_YOUR_NODE"
port = "8078"

[state_chain]
ws_endpoint = "ws://127.0.0.1:9944"
signing_key_file = "/etc/chainflip/keys/signing_key_file"

[eth]
# Ethereum RPC endpoints (websocket and http for redundancy).
ws_node_endpoint = "WSS_ENDPOINT_FROM_ETHEREUM_CLIENT_FROM_SERVICE_A"
http_node_endpoint = "HTTPS_ENDPOINT_FROM_ETHEREUM_CLIENT_FROM_SERVICE_B"

# Ethereum private key file path. This file should contain a hex-encoded private key.
private_key_file = "/etc/chainflip/keys/ethereum_key_file"

[signing]
db_file = "/etc/chainflip/data.db"

[dot]
ws_node_endpoint = "wss://rpc-pdot.chainflip.io:443"
http_node_endpoint = "https://rpc-pdot.chainflip.io:443"

[btc]
http_node_endpoint = "http://a108a82b574a640359e360cf66afd45d-424380952.eu-central-1.elb.amazonaws.com"
rpc_user = "flip"
rpc_password = "flip"

```

### Pointing to the Ethereum Client

If you followed the instructions in [Prerequisites](prerequisites.md), you will probably have an account with an Ethereum RPC provider (Alchemy, Infura, Rivet, etc.). You must change the `ws_node_endpoint` and `http_node_endpoint` in the above file to RPC URLs given to you by these services. You must have both a Websocket (wss) and HTTPS connection in order for the software to function.

{% hint style="info" %}
**Important:** Make sure that you are not using a mainnet RPC, this will not work!
{% endhint %}

You can try other node connections as well. [Geth with an open websocket server enabled](https://geth.ethereum.org/docs/rpc/server#websocket-server) will accept connections through port 8546 by default. If it's running on the same machine as your validator, you can use \[`ws|http]://127.0.0.1:854[6|5]`

### Saving the File

Once you're happy with your configuration file and have inserted a valid endpoint address, exit and save the file by using `CTRL(Control)+x` and when prompted type `Y` then hit `Enter`.

