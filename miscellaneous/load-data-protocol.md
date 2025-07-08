---
description: Using load:// data retrieval protocol
---

# load:// Data Protocol

### About load://

Load Network Data Retriever (load://) is a protocol for retrieving data from the Load Network (EVM). It leverages the LN DA layer and Arweave’s permanent storage to provide trustless access LN transaction data through both networks, whether that’s data which came from LN itself, or L2 data that was settled to LN.

Many chains solve this problem by providing query interfaces to archival nodes or centralized indexers. For Load Network, Arweave _is_ the archival node, and can be queried without special tooling. However, the data LN stores on Arweave is also encoded, serialized and compressed, making it cumbersome to access. The load:// protocol solves this problem by providing an out-of-the-box way to grab and decode Load Network data while also checking it has been DA-verified.

### How it works

The data retrieval pipeline ensures that when you request data associated with a Load Network transaction, it passes through at least one DA check (currently through LN's self-DA).

It then retrieves the transaction block from Arweave, published by LN ExExes, decodes the block (decompresses Brotli and deserializes Borsh), and scans the archived sealed block transactions within LN to locate the requested transaction ID, ultimately returning the calldata (input) associated with it.

<figure><img src="../.gitbook/assets/wvm-req (2).png" alt=""><figcaption><p>workflow</p></figcaption></figure>

### Try it out

Currently, the load:// gateway server provides two methods: one for general data retrieval and another specifically for transaction data posted by the load-archiver nodes. To retrieve calldata for any transaction on Load Network, you can use the following command:

```bash
curl -X GET https://gateway.load.network/calldata/$LN_TXID
```

The second method is specific to `load-archiver` nodes because it decompresses the calldata and then deserializes its Borsh encoding according to a predefined structure. This is possible because the data encoding of load-archiver data is known to include an additional layer of Borsh-Brotli encoding before the data is settled on LN.

```bash
curl -X GET https://gateway.load.network/war-calldata/$LN_TXID
```

### Benchmarks

#### Latency for /calldata

The latency includes the time spent fetching data from LN EVM RPC and the Arweave gateway, as well as the processing time for Brotli decompression, Borsh deserialization, and data validity verification.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption><p>/calldata endpoint benchmark</p></figcaption></figure>

#### Check out the load:// data protocol protocol [here](https://github.com/weavevM/wvm-data-retriever)
