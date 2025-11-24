---
description: Explore the first temporal data storage layer on AO HyperBEAM
---

# Load S3 Layer (LS3)

### About

The Load S3 storage layer is built on top of HyperBEAM as a device, and ao network for data programmability. At its core, the `~s3@1.0` device – a HyperBEAM s3 object-storage provider – is the heart of the storage layer.

The HyperBEAM S3 device offers maximum flexibility for HyperBEAM node operators, allowing them to either spin up MinIO clusters in the same location as the HyperBEAM node and rent their available storage, or connect to existing external clusters, offering native integration between hb’s s3 and devs existing storage clusters. For instance, Load’s S3 device is co-located with the MinIO clusters.

{% hint style="success" %}
To start using the Load S3 temporary storage layer today, checkout the [LCP](cloud-platform-lcp.md), [LS3 with load\_acc](ls3-with-load_acc.md) and the [Turbo upload service](turbo-offchain-upload-service.md) sections.
{% endhint %}

### Erasure-coded redundancy, fault tolerance, and data availability

Load S3’s MinIO cluster, forming the storage layer, runs on 4 nodes with erasure coding enabled. Data is split into data and parity blocks, then striped across all nodes. This allows the system to tolerate the loss of up to two nodes without data loss or service interruption. Unlike full replication, which stores complete copies of each object on multiple nodes, erasure coding provides redundancy with lower storage overhead, ensuring durability while keeping capacity usage efficient.

A four-node configuration also enables automatic data healing. When a failed node comes back online or a new node replaces it, missing blocks are rebuilt from the remaining healthy nodes in real-time, without taking the cluster offline. Object integrity is verified using per-object checksums, and data availability can be asserted using S3 metadata, such as size, timestamp, and ETag – ensuring each object is present, intact, and retrievable.

The Load S3 layer inherits these guarantees by offloading them to a battle-tested distributed object storage system, in this implementation, MinIO. In the future, the Load S3 decentralized network, consisting of multiple S3 HyperBEAM nodes, will have these properties available out of the box, without the need to re-engineer them from scratch.

### \~s3@1.0 & ANS-104 DataItems

The \~s3@1.0 device has been designed with a built-in data protocol to natively handle ANS-104 DataItems offchain temporary storage. This approach translates our rationale: HyperBEAM s3 nodes can store signed & valid ANS-104 DataItems temporarily, that can be pushed anytime, when needed, to Arweave, while maintaining the DataItem’s provenance and determinism (e.g. ID, signature, timestamp, etc). Learn more about [xANS-104](https://blog.load.network/xans-104/) data provenance, lineage and governance here.

### Hybrid Gateway

{% hint style="success" %}
For a direct low level ANS-104 DataItem data streaming from LS3, use the HyperBEAM [sidecar](https://app.gitbook.com/u/9f5jQHFG1jWXf1Txd6jCIxFWKHD2) gateway instead — access the offchain dataitem under: [https://gateway.s3-node-1.load.network/resolve/:dataitem\_id](https://gateway.s3-node-1.load.network/resolve/:dataitem_id)
{% endhint %}

Given the S3 device’s native integration with objects serialized and stored as ANS-104 DataItems, we considered DataItem accessibility, such as resolving via Arweave gateways.

Being an S3 device, we were able to benefit from HyperBEAM’s modular architecture, so we extended HyperBEAM’s gateway: we built the [`hb_gateway_s3.erl`](https://github.com/loadnetwork/load_hb/blob/s3-edge/src/hb_gateway_s3.erl) module and extended the [`hb_gateway_client.erl`](https://github.com/loadnetwork/load_hb/blob/s3-edge/src/hb_gateway_client.erl) by integrating the `hb_gateway_s3` store module as a fallback extension to the Arweave’s GraphQL API.

Additionally, `hb_opts.erl` Stores orders have been modified to add s3 offchain dataitems retrieval as a fallback after HyperBEAM’s cache module, Arweave gateway then S3 (offchain) – offchain DataItems should have the `Data-Protocol : Load-S3` tag to be recognized by the subindex.

Building these extension components, a hb node running the \~[s3@1.0](mailto:s3@1.0) device, benefit from the Hybrid Gateway that can resolve both onchain and offchain dataitems.

#### Load S3 Trust Assumptions, Optimismo

{% hint style="success" %}
For a higher level trust assumption, upload data to LS3 using the Turbo-compliant [upload service](turbo-offchain-upload-service.md) which comes with a built-in system of signed receipts.
{% endhint %}

In the current release, Load S3 is a storage layer consisting of a single centralized yet verifiable storage provider (HyperBEAM node running the \~s3@1.0 device components).

This early-stage testing layer offers similar trust assumptions offered by other centralized services in the Arweave ecosystem such as ANS-104 Bundlers. Load S3’s gradual evolution from a layer to decentralized network built on top of ao network will remove the centralized and trust-based components, one by one, to reach a trustless, verifiable and incentivized temporal data storage network.

### Blazingly Fast ANS-104 DataItems streaming sidecar

Besides the Hybrid Gateway, nodes like `s3-node-1` support a highly optimized low-level dataitems streaming leveraging precomputed dataitem data start-byte offset and range streaming from the S3 cluster directly.

The sidecar bypasses the technical need to deserialize the dataitem in order to extract useful information such as tags and dataitem's data, reducing dramatically the latency for resolving.

On `s3-node-1` — the sidecar is available under `https://gateway.s3-node-1.load.network/resolve/:offchain-dataitemid`&#x20;

And to download the full LS3 DataItem binary (the .ans104 file), you can use the following endpoint: `https://gateway.s3-node-1.load.network/binary/:offchain-dataitemid`

### Developer Guide

Load’s HyperBEAM node running the \~s3@1.0 device is available the following endpoint: [https://s3-node-1.load.network](https://s3-node-1.load.network/) – developers looking to use the HyperBEAM node as S3 endpoint, can use the official S3 SDKs as long as the used S3 commands are supported by `~s3@1.0 - (1:1 parity)` &#x20;

#### Available Endpoints

| Node Name                   | Endpoint                                                               | Features                                                                                                                                                                                                                                                                                                        |
| --------------------------- | ---------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| S3 Node 1 (current testnet) | [https://s3-node-1.load](https://s3-node-1.load/)                      | <ul><li>untouched ao compute (canonical)</li><li>Blazingly fast ANS-104 dataitems streaming from S3 (sidecar)</li><li>Hybrid Gateway</li><li>Powers LCP, Turbo upload service and load-s3-agent</li><li>x402 integration</li><li>supports private, expirable shareable ANS-104 DataItems (S3 objects)</li></ul> |
| ~~S3 Node 0 (deprecated)~~  | [~~https://s3-node-0.load.network~~ ](https://s3-node-0.load.network/) | <ul><li><del>ao compute with offchain dataitems</del></li><li><del>Hybrid Gateway</del></li></ul>                                                                                                                                                                                                               |

To learn how to start using Load S3 today, check out the following [page](ls3-with-load_acc.md)
