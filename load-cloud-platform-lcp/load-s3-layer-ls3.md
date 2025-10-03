---
description: Explore the first temporal data storage layer on AO HyperBEAM
---

# Load S3 Layer

### About

The Load S3 storage layer is built on top of HyperBEAM as a device, and ao network for data programmability. At its core, the `~s3@1.0` device – a HyperBEAM s3 object-storage provider – is the heart of the storage layer.

The HyperBEAM S3 device offers maximum flexibility for HyperBEAM node operators, allowing them to either spin up MinIO clusters in the same location as the HyperBEAM node and rent their available storage, or connect to existing external clusters, offering native integration between hb’s s3 and devs existing storage clusters. For instance, Load’s S3 device is co-located with the MinIO clusters.

{% hint style="info" %}
The beta release for Load S3, an object-storage temporal data storage layer with \~300TB of storage space available to be rented.
{% endhint %}

### Erasure-coded redundancy, fault tolerance, and data availability

Load S3’s MinIO cluster, forming the storage layer, runs on 4 nodes with erasure coding enabled. Data is split into data and parity blocks, then striped across all nodes. This allows the system to tolerate the loss of up to two nodes without data loss or service interruption. Unlike full replication, which stores complete copies of each object on multiple nodes, erasure coding provides redundancy with lower storage overhead, ensuring durability while keeping capacity usage efficient.

A four-node configuration also enables automatic data healing. When a failed node comes back online or a new node replaces it, missing blocks are rebuilt from the remaining healthy nodes in real-time, without taking the cluster offline. Object integrity is verified using per-object checksums, and data availability can be asserted using S3 metadata, such as size, timestamp, and ETag – ensuring each object is present, intact, and retrievable.

The Load S3 layer inherits these guarantees by offloading them to a battle-tested distributed object storage system, in this implementation, MinIO. In the future, the Load S3 decentralized network, consisting of multiple S3 HyperBEAM nodes, will have these properties available out of the box, without the need to re-engineer them from scratch.

### \~s3@1.0 & ANS-104 DataItems

The \~s3@1.0 device has been designed with a built-in data protocol to natively handle ANS-104 DataItems offchain temporary storage. This approach translates our rationale: HyperBEAM s3 nodes can store signed & valid ANS-104 DataItems temporarily, that can be pushed anytime, when needed, to Arweave, while maintaining the DataItem’s provenance and determinism (e.g. ID, signature, timestamp, etc).

### Hybrid Gateway

Given the S3 device’s native integration with objects serialized and stored as ANS-104 DataItems, we considered DataItem accessibility, such as resolving via Arweave gateways.

Being an S3 device, we were able to benefit from HyperBEAM’s modular architecture, so we extended HyperBEAM’s gateway: we built the [`hb_gateway_s3.erl`](https://github.com/loadnetwork/load_hb/blob/s3-edge/src/hb_gateway_s3.erl) module and extended the [`hb_gateway_client.erl`](https://github.com/loadnetwork/load_hb/blob/s3-edge/src/hb_gateway_client.erl) by integrating the `hb_gateway_s3` store module as a fallback extension to the Arweave’s GraphQL API.

Additionally, `hb_opts.erl` Stores orders have been modified to add s3 offchain dataitems retrieval as a fallback after HyperBEAM’s cache module, Arweave gateway then S3 (offchain) – offchain DataItems should have the `Data-Protocol : Load-S3` tag to be recognized by the subindex.

Building these extension components, a hb node running the \~[s3@1.0](mailto:s3@1.0) device, benefit from the Hybrid Gateway that can resolve both onchain and offchain dataitems.

#### Load S3 Trust Assumptions, Optimismo

In the current release, Load S3 is a storage layer consisting of a single centralized yet verifiable storage provider (HyperBEAM node running the \~s3@1.0 device components).

This early-stage testing layer offers similar trust assumptions offered by other centralized services in the Arweave ecosystem such as ANS-104 Bundlers. Load S3’s gradual evolution from a layer to decentralized network built on top of ao network will remove the centralized and trust-based components, one by one, to reach a trustless, verifiable and incentivized temporal data storage network.

### Blazingly Fast ANS-104 DataItems streaming sidecar

Besides the Hybrid Gateway, nodes like `s3-node-1` support a highly optimized low-level dataitems streaming leveraging precomputed dataitem data start-byte offset and range streaming from the S3 cluster directly.

The sidecar bypasses the technical need to deserialize the dataitem in order to extract useful information such as tags and dataitem's data, reducing dramatically the latency for resolving.

On `s3-node-1` — the sidecar is available under `https://gateway.s3-node-1.load.network/resolve/:offchain-dataitemid`&#x20;

### Developer Guide

Load’s HyperBEAM node running the \~s3@1.0 device is available the following endpoint: [https://s3-node-0.load.network](https://s3-node-0.load.network/) – developers looking to use the HyperBEAM node as S3 endpoint, can use the official S3 SDKs as long as the used S3 commands are supported by `~s3@1.0`

#### Available Endpoints

| Node Name | Endpoint                                                           | Features                                                                                                                             |
| --------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| S3 Node 0 | [https://s3-node-0.load.network ](https://s3-node-0.load.network/) | <ul><li>ao compute with offchain dataitems</li><li>Hybrid Gateway</li></ul>                                                          |
| S3 Node 1 | [https://s3-node-1.load](https://s3-node-1.load/)                  | <ul><li>untouched ao compute (canonical)</li><li>Blazingly fast ANS-104 dataitems streaming from S3</li><li>Hybrid Gateway</li></ul> |

#### Installation

#### NodeJS

To install the official S3 library in NodeJS, run the following command

```shell
$ yarn add @aws-sdk/client-s3
```

**Initialization**

In order to initialize the S3 client connected to the HyperBEAM node, you can do the following:

```typescript
import { S3Client } from "@aws-sdk/client-s3";

const accessKeyId = process.env.LOAD_ACCESS_KEY;
const secretAccessKey = process.env.LOAD_SECRET_ACCESS_KEY; 

const s3Client = new S3Client({
    region: "eu-west-2", // Required -- current supported region
    endpoint: "https://s3-node-0.load.network/~s3@1.0", // Load.Network HB S3 endpoint
    credentials: {
        accessKeyId,
        secretAccessKey,
    },
    forcePathStyle: true, // Required
});
```

### Rust Examples

```rust
use aws_sdk_s3::error::SdkError;
use aws_sdk_s3::operation::create_bucket::CreateBucketError;
use aws_sdk_s3::Client;

pub async fn create_client() -> Client {
    let config = aws_config::from_env()
        .endpoint_url("https://s3-node-0.load.network/~s3@1.0")
        .region("eu-west-2")
        .load()
        .await;

    let s3_config = aws_sdk_s3::config::Builder::from(&config)
        .force_path_style(true)
        .build();

    Client::from_conf(s3_config)
}

pub async fn s3_create_bucket() -> Result<(), SdkError<CreateBucketError>> {
    let client = create_client().await;
    
    match client.create_bucket()
        .bucket("LoadNetworkBucketTest")
        .send()
        .await {
            Ok(output) => {
                println!("✅ Bucket created: {}", output.location().unwrap_or("(no location)"));
                Ok(())
            },
            Err(err) => {
                println!("❌ Error creating bucket: {}", err);
                Err(err)
            }
    }
}
```

If you wish to beta get access, reach out to us on [Telegram](https://t.me/loadnetwork) – for more documentation on the \~s3@1.0 device, check the [device documentation](../load-hyperbeam/s3-1.0-device.md).
