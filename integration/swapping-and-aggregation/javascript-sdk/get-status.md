---
description: Fetches the status of a swap
---

# Get Status

### getStatus

Fetches the status of an ongoing swap based on the provided `swapStatusRequest` argument.

```typescript
getStatus(
  swapStatusRequest: SwapStatusRequest, 
  options?: RequestOptions
): Promise<SwapStatusResponse>
```

The `SwapStatusRequest` object includes the following arguments:

<table><thead><tr><th width="123">Param</th><th width="372">Description</th><th width="244.33333333333331">Data type</th></tr></thead><tbody><tr><td><code>id</code></td><td><p>One of the following:</p><ul><li>The <code>depositChannelId</code> from <a data-mention href="swap-assets/request-deposit-address.md">request-deposit-address.md</a> method</li><li>The <code>transactionHash</code> from the <a data-mention href="swap-assets/execute-swap.md">execute-swap.md</a> method</li></ul></td><td>string</td></tr></tbody></table>

The response will include the following:

```typescript
interface CommonStatusFields {
  srcChain: Chain;
  destChain: Chain;
  srcAsset: Asset;
  destAsset: Asset;
  destAddress: string;
  depositAddress: string;
  expectedDepositAmount: string;
}

export type SwapStatusResponse = CommonStatusFields &
  (
    | { state: 'AWAITING_DEPOSIT' }
    | {
        state: 'DEPOSIT_RECEIVED';
        depositAmount: string;
        depositReceivedAt: number;
        depositReceivedBlockIndex: string;
      }
    | {
        state: 'SWAP_EXECUTED';
        depositAmount: string;
        depositReceivedAt: number;
        depositReceivedBlockIndex: string;
        intermediateAmount: string | undefined;
        swapExecutedAt: number;
        swapExecutedBlockIndex: string;
      }
    | {
        state: 'EGRESS_SCHEDULED';
        depositAmount: string;
        depositReceivedAt: number;
        depositReceivedBlockIndex: string;
        intermediateAmount: string | undefined;
        swapExecutedAt: number;
        swapExecutedBlockIndex: string;
        egressAmount: string;
        egressScheduledAt: number;
        egressScheduledBlockIndex: string;
      }
    | {
        state: 'BROADCAST_REQUESTED';
        depositAmount: string;
        depositReceivedAt: number;
        depositReceivedBlockIndex: string;
        intermediateAmount: string | undefined;
        swapExecutedAt: number;
        swapExecutedBlockIndex: string;
        egressAmount: string;
        egressScheduledAt: number;
        egressScheduledBlockIndex: string;
        broadcastRequestedAt: number;
        broadcastRequestedBlockIndex: string;
      }
    | {
        state: 'BROADCAST_ABORTED';
        depositAmount: string;
        depositReceivedAt: number;
        depositReceivedBlockIndex: string;
        intermediateAmount: string | undefined;
        swapExecutedAt: number;
        swapExecutedBlockIndex: string;
        egressAmount: string;
        egressScheduledAt: number;
        egressScheduledBlockIndex: string;
        broadcastRequestedAt: number;
        broadcastRequestedBlockIndex: string;
        broadcastAbortedAt: number;
        broadcastAbortedBlockIndex: string;
      }
    | {
        state: 'COMPLETE';
        depositAmount: string;
        depositReceivedAt: number;
        depositReceivedBlockIndex: string;
        intermediateAmount: string | undefined;
        swapExecutedAt: number;
        swapExecutedBlockIndex: string;
        egressAmount: string;
        egressScheduledAt: number;
        egressScheduledBlockIndex: string;
        broadcastRequestedAt: number;
        broadcastRequestedBlockIndex: string;
        broadcastSucceededAt: number;
        broadcastSucceededBlockIndex: string;
      }
  );
```

### Example

Here is an example using [request-deposit-address.md](swap-assets/request-deposit-address.md "mention"):

```javascript
const swapStatusRequest = {
  id: '1234567890', // depositChannelId or transactionHash
};

console.log(await swapSDK.getStatus(swapStatusRequest));
```

### Sample Response

```json
{
  "broadcastAbortedBlockIndex": null,
  "broadcastRequestedAt": 1669907147201,
  "broadcastRequestedBlockIndex": "202-4",
  "broadcastSucceededAt": 1669907153201,
  "broadcastSucceededBlockIndex": "204-4",
  "depositAddress": "0x6Aa69332B63bB5b1d7Ca5355387EDd5624e181F2",
  "depositAmount": "1000000000000",
  "depositReceivedAt": 1669907135201,
  "depositReceivedBlockIndex": "100-3",
  "destAddress": "5F3sa2TJAWMqDhXG6jhV4N8ko9SxwGy8TpaNS1repo5EYjQX",
  "destAsset": "DOT",
  "destChain": "Polkadot",
  "egressAmount": "1000000000000000000",
  "egressScheduledAt": 1669907147201,
  "egressScheduledBlockIndex": "202-3",
  "expectedDepositAmount": "10000000000",
  "intermediateAmount": "20000000",
  "srcAsset": "ETH",
  "srcChain": "Ethereum",
  "state": "COMPLETE",
  "swapExecutedAt": 1669907141201,
  "swapExecutedBlockIndex": "200-3",
}
```

