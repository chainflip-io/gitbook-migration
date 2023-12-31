---
description: Fetches all supported tokens
---

# Get Assets

### getAssets

Fetches the list of available assets for the given `chain`.

```javascript
getAssets(chain: Chain): Promise<AssetData[]>
```

<table><thead><tr><th width="223">Param</th><th width="372">Description</th><th width="244.33333333333331">Data type</th></tr></thead><tbody><tr><td><code>chain</code></td><td>The ID of the chain the returned assets</td><td>string</td></tr></tbody></table>

### Example

```javascript
import { Chains } from '@chainflip-io/chainflip-sdk/swap';

console.log(await swapSDK.getAssets(Chains.Ethereum));
```

### Sample Response

```json
[
  {
    "id": "ETH",
    "chain": "Ethereum",
    "contractAddress": "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2",
    "decimals": 18,
    "name": "Ether",
    "symbol": "ETH",
    "isMainnet": true,
    "minimumDepositAmount": "0",
    "minimumSwapAmount": "1000000000000000000",
  },
  ...,
  {
    "id": "USDC",
    "chain": "Ethereum",
    "contractAddress": "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2",
    "decimals": 6,
    "name": "USDC Stablecoin",
    "symbol": "USDC",
    "isMainnet": true,
    "minimumDepositAmount": "0",
    "minimumSwapAmount": "1000000",
  }
```
