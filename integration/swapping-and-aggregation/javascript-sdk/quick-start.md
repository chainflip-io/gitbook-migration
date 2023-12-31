---
description: Installing the Chainflip SDK
---

# Quick Start

## Installation

The Chainflip SDK is a lightweight npm package. You can install it here:

```bash
npm install --save @chainflip-io/chainflip-sdk
```

## Set up

To start using the _Chainflip SDK_, you'll first need to import it and create an instance with the appropriate `options`:

```javascript
import { SwapSDK } from '@chainflip-io/chainflip-sdk/swap';
import { Wallet } from 'ethers';

const options = {
  network: 'partnernet', // best for partners
  backendServiceUrl: 'https://example.chainflip.io', // optional 
  signer: Wallet.fromMnemonic(process.env.WALLET_MNEMONIC) // optional 
};

const swapSDK = new SwapSDK(options);

```

The `options` object accepts the following properties:

<table><thead><tr><th width="223">Param</th><th width="476">Description</th><th width="244.33333333333331">Data type</th></tr></thead><tbody><tr><td><code>network</code></td><td>The Chainflip network to interact with. Defaults to <code>perseverance</code>. See <a data-mention href="../supported-chains-and-assets.md#chainflip-networks">#chainflip-networks</a>.</td><td><code>'sisyphos' | 'perseverance' | 'mainnet' |</code><br><code>'partnernet'</code></td></tr><tr><td><code>backendServiceUrl</code></td><td>The URL of the backend service. Defaults to the backend service run by Chainflip for the given network.</td><td>string</td></tr><tr><td><code>signer</code></td><td>An instance of an <code>ethers</code> signer. Only needed for calls that use smart contracts.</td><td><code>Signer</code></td></tr></tbody></table>
