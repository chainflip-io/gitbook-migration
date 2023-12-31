---
description: Fetches all supported chains
---

# Get Chains

### getChains

Returns the list of available chains or destination chains for the provided `sourceChain`. If no `sourceChain` is provided, it returns all available chains.

```typescript
getChains(sourceChain?: Chain): Promise<ChainData[]>
```

<table><thead><tr><th width="223">Param</th><th width="372">Description</th><th width="244.33333333333331">Data type</th></tr></thead><tbody><tr><td><code>sourceChain</code></td><td>Chain ID that allows to filter the returned chains (optional)</td><td>string</td></tr></tbody></table>

### Example

```typescript
console.log(await swapSDK.getChains());
```

### Sample Response

```json
[
  {
    "id": "Ethereum",
    "name": "Ethereum",
    "isMainnet": true
  },
  {
    "id": "Polkadot",
    "name": "Polkadot",
    "isMainnet": true
  },
  {
    "id": "Bitcoin",
    "name": "Bitcoin",
    "isMainnet": true
  }
]
```

