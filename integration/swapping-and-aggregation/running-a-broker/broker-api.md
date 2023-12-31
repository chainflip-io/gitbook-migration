# Broker API

The Broker API is a standalone client that exposes _Broker_ functionality via a JSON API interface.&#x20;

Brokers need to run the client for the API themselves, as the Broker holds keys used to sign extrinsics and collect any fees set for Deposit Channels. The API client works by pointing it to an RPC node — also run by the same Broker, ideally.

## Command line arguments and defaults

* The `state_chain.ws_endpoint` should point at a **synced Chainflip State Chain RPC node**. The default is `ws://localhost:9944`assuming it is run locally.
* The `state_chain.signing_key_file` should be the **Broker's private key** for their on-chain account. The account should be [funded](http://127.0.0.1:5000/s/jk6cWeoa0h57DFwHlHoV/funding/funding#funding-your-account-via-the-validator-dashboard). The account type should be set to Broker. The default is `/etc/chainflip/keys/signing_key_file`.
* The `port` is the port on which the Broker will listen for connections. Use `0` to assign a random port. The default is `80`.

```bash
> ./target/release/chainflip-broker-api --help

chainflip-broker-api

USAGE:
    chainflip-broker-api [OPTIONS]

OPTIONS:
    -h, --help
            Print help information

        --port <PORT>
            The port number on which the broker will listen for connections. Use 0 to assing a
            random port. [default: 80]

        --state_chain.signing_key_file <SIGNING_KEY_FILE>
            A path to a file that contains the broker's secret key for signing extrinsics.
            [default: /etc/chainflip/keys/signing_key_file]

        --state_chain.ws_endpoint <WS_ENDPOINT>
            The state chain node's rpc endpoint. [default: ws://localhost:9944]
```

## RPC Methods

### `broker_requestSwapDepositAddress`

Parameters:

* Source asset as a camel-case string, eg "Eth" or "Dot".
* Egress asset as a camel-case string, eg "Eth" or "Dot".
* Egress Address in hex. Must match the format of the egress asset's chain: 20 bytes for Ethereum assets, 32 bytes for Polkadot.
* Broker Commission in basis points (100th of a percent). Broker operators can choose to charge a fee for the use of their endpoint, and can be set at any value from 1 basis point to 1000 basis points.&#x20;

Return:

* Hex-encoded deposit address.

### `broker_registerAccount`

Parameters:

None

Return:

* `null` if successful, otherwise an error.

## Working Example

{% hint style="info" %}
This example assumes the node that is exposing the State Chain RPC is **funded**.
{% endhint %}

1. Open a terminal and run:

```bash
> ./target/release/chainflip-broker-api \
    --state_chain.ws_endpoint=ws://localhost:9944 \
    --state_chain.signing_key_file /path/to/my/signing_key \
    --port 62378 # or whatever port you want to use

🎙 Server is listening on 0.0.0.0:62378.
```

2. Open another terminal and run:

```bash
# This method might not be necessary/useful depending on how we set up the broker.
> curl -H "Content-Type: application/json" \
    -d '{"id":1, "jsonrpc":"2.0", "method": "broker_registerAccount"}' \
    http://localhost:62378

{"jsonrpc":"2.0","result":null,"id":1}

# This method takes a little while to respond because it submits and waits for finality. So make sure the request doesn't block.
# Parameters are: [source_asset, destination_asset, destination_address, broker_commission].
> curl -H "Content-Type: application/json" \
    -d '{"id":1, "jsonrpc":"2.0", "method": "broker_requestSwapDepositAddress", "params": ["Eth", "Flip","0xabababababababababababababababababababab", 0]}' \
    http://localhost:62378

# The result is the hex-encoded deposit address, expiry block, and the issued block.
{"jsonrpc":"2.0","result":{"address":"0x4ef7608893d5a06c2689b8d15b4dc400be0954f2",expiry_block:12345},"id":1}

# This request also accepts cross-chain message metadata as an optional fifth parameter:
{"gas_budget":"0x1000", message:[0,1,2,3,4], cf_parameters: [], source_address: "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa", source_chain: "Bitcoin"}
```

## Limitations

* It doesn't seem to work with **`wss`**, so make sure the address is specified with **`ws`**. It should be ok since we're not going to expose this externally.
