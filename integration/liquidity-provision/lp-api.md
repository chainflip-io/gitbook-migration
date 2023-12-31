# LP API

{% hint style="warning" %}
These docs are under active development. 🚧
{% endhint %}

The LP API bundle allows liquidity providers to put up orders programmatically through the JIT AMM. This is possible by connecting to the _State Chain_ client using **Websockets** and **RPC** calls, typical of centralised exchanges. This also removes the need for complex middleware to translate blockchain activity into instructions compatible with typical market-making software — unlike other DEXes.

The LP API exposes this functionality via a JSON API interface.

## Command line arguments and defaults

* The `ws_endpoint` should point at a synced RPC node. The `signing_key_file` should be the **Broker's private key** for their on-chain account. The account should be [funded](http://127.0.0.1:5000/s/jk6cWeoa0h57DFwHlHoV/funding/funding).

```bash
> ./target/release/chainflip-lp-api --help

chainflip-lp-api

USAGE:
    chainflip-lp-api [OPTIONS]

OPTIONS:
    -h, --help
            Print help information

        --state_chain.signing_key_file <SIGNING_KEY_FILE>
            [default: /etc/chainflip/keys/signing_key_file]

        --state_chain.ws_endpoint <WS_ENDPOINT>
            [default: ws://localhost:9944]
```

## RPC Methods

### `lp_registerAccount`

Parameters:

None

Return:

* `null` if successful, otherwise an error

### `lp_liquidityDeposit`

Parameters:

* Source asset as a camel-case string, eg "Eth" or "Dot"

Return:

* Hex encoded _deposit address._

### `lp_withdrawAsset`

Parameters:

* Asset amount as u128
* Egress asset as a camel-case string, eg "Eth" or "Dot"
* Egress Address in Hex. Must match the format of the egress asset's chain: 20 bytes for Ethereum assets, 32 bytes for Polkadot.

Return:

* Egress id

### `lp_mintRangeOrder`

Parameters:

* Asset as a camel-case string, eg "Eth" or "Dot"
* Lower tick as i32
* Upper tick as i32
* A JSON object specifying the exact amount of liquidity. This can be provided in two different ways:
  1. &#x20;`{"PoolLiquidity": <amount>}` where `<amount>` is a u128 or a hex string specifying the size of the order in microUSDC
  2. &#x20;`{"AssetAmounts":{"desired":{"unstable": <amount1>, "stable": <amount2>}, "minimum":{"unstable": <amount3>, "stable: <amount4>}}}` where the `<amounts>` are u128 or hex strings specifying the size of the order in microUSDC. The "stable" amount always refers to USDC, and the "unstable" to the Asset specified in the first argument. Using "AssetAmounts", the system will try to mint an order up to the "desired" amounts, but will not mint less than the "minimum" amounts.

Return:

* assets\_debited
  * Asset\_0
  * Asset\_1
* fees\_harvested
  * Asset\_0
  * Asset\_1

### `lp_burnRangeOrder`

Parameters:

* Asset as a camel-case string, eg "Eth" or "Dot"
* Lower tick as i32
* Upper tick as i32
* Asset amount as u128

Return:

* assets\_returned
  * Asset\_0
  * Asset\_1
* fees\_harvested
  * Asset\_0
  * Asset\_1

### `lp_mintLimitOrder`

Parameters:

* Asset as a camel-case string, eg "Eth" or "Dot"
* Order as a camel-case string, "Buy" or "Sell"
* Price tick as i32
* Asset amount as u128

Return:

* assets\_debited
* collected\_fees
* swapped\_liquidity

### `lp_burnLimitOrder`

Parameters:

* Asset as a camel-case string, eg "Eth" or "Dot"
* Order as a camel-case string, "Buy" or "Sell"
* Price tick as i32
* Asset amount as u128

Return:

* assets\_credited
* collected\_fees
* swapped\_liquidity

### <mark style="color:blue;">`lp_tokenBalances`</mark>`*`

Parameters:

None

Return:

* A list of all assets and their free balance in JSON format

{% hint style="info" %}
Coming soon. This functionality is not implemented yet.
{% endhint %}

### <mark style="color:blue;">`lp_getRangeOrders`</mark>`*`

Parameters:

None

Return:

* A list of all assets and their range order positions in JSON format

{% hint style="info" %}
Coming soon. This functionality is not implemented yet.
{% endhint %}

## Working Example

```bash
./target/release/chainflip-lp-api \
 --state_chain.ws_endpoint=ws://localhost:9944 \
 --state_chain.signing_key_file /path/to/my/signing_key

🎙 Server is listening on 0.0.0.0:80.
```

Default values are `ws://localhost:9944` and `/etc/chainflip/keys/signing_key_file`

Then in another terminal:

```bash
> curl -H "Content-Type: application/json" \
    -d '{"id":1, "jsonrpc":"2.0", "method": "lp_registerAccount", "params": [0]}' \
    http://localhost:80

{"jsonrpc":"2.0","result":null,"id":1}

# This method take a little while to respond because it submits and waits for finality. So make sure the request doesn't block.
# Parameters are: [ asset ].
> curl -H "Content-Type: application/json" \
    -d '{"id":1, "jsonrpc":"2.0", "method": "lp_liquidityDeposit", "params": ["Eth"]}' \
    http://localhost:80

# The result is the hex-encoded deposit address.
{"jsonrpc":"2.0","result":"0x350ec3dfd773978277868212d9f1319cbc93a8bf","id":1}
```

## Limitations

* It doesn't seem to work with **`wss`**, so make sure the address is specified with **`ws`**. It should be ok since we're not going to expose this externally.
