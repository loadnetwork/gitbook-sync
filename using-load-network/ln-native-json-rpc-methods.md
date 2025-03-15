---
description: About Load Network Native JSON-RPC methods
---

# LN-Native JSON-RPC Methods

### The `eth_getArweaveStorageProof` JSON-RPC method

This JSON-RPC method lets you retrieve the Arweave storage proof for a given Load Network block number

```bash
curl -X POST https://alphanet.load.network \
-H "Content-Type: application/json" \
--data '{
 "jsonrpc":"2.0",
 "method":"eth_getArweaveStorageProof",
 "params":["8038800"],
 "id":1
}'
```

### The `eth_getWvmTransactionByTag` JSON-RPC method

For Load Network L1 tagged transactions, the `eth_getWvmTransactionByTag` lets you retrieve a transaction hash for a given name-value tag pair.

```bash
curl https://alphanet.load.network \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "eth_getWvmTransactionByTag",
    "params": [{
        "tag": ["name", "value"]
    }]
  }'
```
