---
description: The EIP-4844 data agent
---

# Blobscan Agent

The [blobscab-agent](https://github.com/loadnetwork/blobscan-agent) stores Ethereum's blobs temporarily on the [\~s3@1.0 HyperBEAM device](../load-hyperbeam/s3-1.0-device.md), serialized as [ANS-104 DataItems](https://github.com/ArweaveTeam/arweave-standards/blob/master/ans/ANS-104.md). Based on need/demand, DataItems can be deterministically pushed to Arweave while maintaining integrity and provenance.

DataItems stored on the `~s3@1.0` device can be retrieved from the Hybrid Gateway as if they are Arweave txs: [https://github.com/loadnetwork/load\_hb/tree/s3-edge/native/s3\_nif#hybrid-gateway](https://github.com/loadnetwork/load_hb/tree/s3-edge/native/s3_nif#hybrid-gateway)

### Agent Server Methods

Retrieve blob versioned hash and the associated ANS-104 dataitem id by versioned hash

```bash
curl -X GET https://load-blobscan-agent.load.network/v1/blob/$BLOB_VERSIONED_HASH
```

#### Retrieve Indexer stats

```bash
curl -X GET https://load-blobscan-agent.load.network/v1/stats
```

#### Agent's server info

```bash
curl -X GET https://load-blobscan-agent.load.network/v1/info
```
