---
description: Get set up with the onchain data center
icon: bolt
---

# Quickstart

{% hint style="info" %}
To easily feed Load Network docs to your favourite LLM, access the compressed knowledge (aka LLM.txt) file from Load Network: [https://www.llmtxt.xyz/g/loadnetwork/gitbook-sync/9](https://www.llmtxt.xyz/g/loadnetwork/gitbook-sync/9) (last update: 10/09/2025, 08:30:08 UTC)
{% endhint %}

Let's make it easy to get going with Load Network. In this doc, we'll go through the simplest ways to use Load across the most common use cases:

* [Uploading data](quickstart.md#upload-data)
* [Integrating ledger storage](quickstart.md#integrating-ledger-storage)
* [Using Load DA](quickstart.md#using-load-da)
* [Migrate from another storage layer](quickstart.md#migrate-from-another-storage-layer)

### Upload data

#### As a non-developer: LCP

The easiest way to interface with Load Network storage capabilities is through the cloud web app: [cloud.load.network](https://cloud.load.network/), Load Cloud Platform.

<figure><img src=".gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

#### As a developer

#### Load S3 Temporal Data Storage Layer

The best data pipeline for massive uploads is to use the Load [\~s3@1.0 HyperBEAM](load-cloud-platform-lcp/load-s3-layer.md) device that creates S3 objects serialized as ANS-104 DataItems, maintaining provenance and integrity when the uploader wishes to move the S3 object from the temporal storage layer to Arweave in a single HTTP API request.&#x20;

#### Highly scalable bundling service

To load huge amount of data to Load Network without being tied to the technical network limitations (tx size, block size, network throughput), you can use the load0 bundling service. It's a straightforward REST-based bundling service that let you upload data and retrieve it instantly, at scale:

```bash
curl -X POST "https://load0.network/upload" \
     --data-binary "@./video.mp4" \
     -H "Content-Type: video/mp4"
```

For more examples, check out the [load0 documentation](using-load-network/miscellaneous/load0-data-layer.md)

#### Direct onchain data bundling

However, if you prefer to directly settle your data onchain via the EVM bundles transaction format (0xbabe), the easiest way to do it is to use an 0xbabe2 bundling service.

The recommended testnet bundling service endpoints are:

* [upload.onchain.rs](https://upload.onchain.rs) (upload)
* [resolver.bot](https://resolver.bot) (retrieve)

Instantiate an uploader in the [bundler-upload-sdk](https://github.com/weaveVM/bundler-upload-sdk) using this endpoint and the public testnet API key:

```bash
API_KEY=d025e132382aea412f4256049c13d0e92d5c64095d1c88e1f5de7652966b69af
```

{% hint style="warning" %}
Limits are in place for the public testnet bundler. For production use at scale, we recommend running your own bundling service as explained [here](https://github.com/weaveVM/bundler), or [get in touch](https://calendly.com/decentlandlabs/founders-chat)
{% endhint %}

#### Full upload example

```javascript
import { BundlerSDK } from 'bundler-upload-sdk';
import { readFile } from 'fs/promises';
import 'dotenv/config';

const bundler = new BundlerSDK('https://upload.onchain.rs/', process.env.API_KEY);

async function main() {
  try {
    const fileBuffer = await readFile('files/hearts.gif');
    const txHash = await bundler.upload([
      {
        file: fileBuffer,
        tags: {
          'content-type': 'image/gif',
        }
      }
    ]);
    console.log(`https://resolver.bot/bundle/${txHash}/0`);
  } catch (error) {
    console.error('Upload failed:', error.message);
    process.exit(1);
  }
}

main().catch(error => {
  console.error('Unhandled error:', error);
  process.exit(1);
});
```

...Or [clone this example repo](https://github.com/weaveVM/bundler-upload-example) to avoid copy-pasting.

#### Want to build your own 0xbabe2 bundling service?

The above example demonstrates posting data in a single Load Network base layer tx. This is limited by Load's blocksize, so tops out at about 8mb.

For practically unlimited onchain upload sizes, you can use the large bundles spec to submit data in chunks. Chunks can even be uploaded in parallel, making large bundles a performant way to handle big uploads.

The [Rust Bundler SDK](https://github.com/weaveVM/bundler?tab=readme-ov-file#0xbabe2-large-bundle) makes it possible for developers to spin up their own bundling services with support for large bundles.

### Integrating ledger storage

Chains like Avalanche, Metis and RSS3 use Load Network as a decentralized archive node. This works by feeding all new and historical blocks to an archiving service you can run yourself, pointed to your network's RPC.

[Clone the archiver repo here](https://github.com/WeaveVM/wvm-archiver)

As well as storing all real-time and historical data, Load Network can be used to reconstruct full chain state, effectively replicating exactly what archive nodes do, but with a decentralized storage layer underneath. Read [here](https://blog.load.network/state-reconstruction/) to learn how.

### Using Load DA

With 125mb/s data throughput and long-term data guarantees, Load Network can handle DA for every known L2, with 99.8% room to spare.

Right now there are 4 ways you can integrate Load Network for DA:

1. [As a blob storage layer for EigenDA](da-integrations/ln-eigenda-proxy-server.md)
2. [As a DA layer for Dymension RollApps](da-integrations/ln-dymension-da-client-for-rollap.md)
3. [As an OP-Stack rollup](load-network-for-evm-chains/deploying-op-stack-rollups.md)
4. DIY

DIY docs are a work in progress, but the [commit](https://github.com/dymensionxyz/dymint/commit/0140460c75bce6dc1cdcaf15527792734a0f7501) to add support for Load Network in Dymension can be used as a guide to implement Load DA elsewhere.&#x20;

{% hint style="info" %}
Work with us to use Load DA for your chain - get onboarded [here](https://calendly.com/decentlandlabs/founders-chat).
{% endhint %}

### Migrate from another storage layer

If your data is already on another storage layer like IPFS, Filecoin, Swarm or AWS S3, you can use specialized importer tools to migrate.

#### Load S3 Storage Layer

The [HyperBEAM Load S3 node](load-cloud-platform-lcp/load-s3-layer.md) provides a 1:1 compatible development interface for applications using AWS S3 for storage, keeping method names and parameters in tact so the only change should be one line: the endpoint.

#### Filecoin&#x20;

The load-lassie import tool is the recommended way to easily migrate data stored via Filecoin.

Just provide the CID you want to import to the API, e.g.:

`https://lassie.load.rs/import/<CID>`

The importer is also self-hostable and further documented [here](https://github.com/weaveVM/wvm-lassie).

#### Swarm

Switching from Swarm to Load is as simple as changing the gateway you already use to resolve content from Swarm.

* before: [https://api.gateway.ethswarm.org/bzz/](https://api.gateway.ethswarm.org/bzz/)\<hash>
* after: [https://swarm.load.rs/bzz/](https://swarm.wvm.network/bzz/)\<hash>

The first time Load's Swarm gateway sees a new hash, it uploads it to Load Network and serves it directly for subsequent calls. This effectively makes your Swarm data permanent on Load while maintaining the same hash.
