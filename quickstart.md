---
description: Get set up with the onchain data center
icon: bolt
---

# Quickstart

{% hint style="info" %}
To easily feed Load Network docs to your favourite LLM, access the compressed knowledge (aka LLM.txt) file from Load Network: [https://gateway.load.rs/bundle/0x5eef8d0f9a71bbee9a566430e6b093f916900b7d6d91d34e5641768db4ee3ef7/0](https://gateway.load.rs/bundle/0x5eef8d0f9a71bbee9a566430e6b093f916900b7d6d91d34e5641768db4ee3ef7/0)&#x20;
{% endhint %}

Let's make it easy to get going with Load Network. In this doc, we'll go through the simplest ways to use Load across the most common use cases:

* [Uploading data](quickstart.md#upload-data)
* [Integrating ledger storage](quickstart.md#integrating-ledger-storage)
* [Using Load DA](quickstart.md#using-load-da)
* [Migrate from another storage layer](quickstart.md#migrate-from-another-storage-layer)

### Upload data

The easiest way to upload data to Load Network is to use a bundling service. Bundling services cover upload costs on your behalf, and feel just like using a web2 API.

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

#### Need to upload a huge amount of data?

The above example demonstrates posting data in a single Load Network base layer tx. This is limited by Load's blocksize, so tops out at about 8mb.

For practically unlimited upload sizes, you can use the large bundles spec to submit data in chunks. Chunks can even be uploaded in parallel, making large bundles a performant way to handle big uploads.

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

#### AWS S3

The [Load S3 SDK](https://github.com/weaveVM/wvm-aws-sdk-s3) provides a 1:1 compatible development interface for applications using AWS S3 for storage, keeping method names and parameters in tact so the only change should be one line: the `import` .

#### Filecoin / IPFS

The load-lassie import tool is the recommended way to easily migrate data stored via Filecoin or IPFS.

Just provide the CID you want to import to the API, e.g.:

`https://lassie.load.rs/import/<CID>`

The importer is also self-hostable and further documented [here](https://github.com/weaveVM/wvm-lassie).

#### Swarm

Switching from Swarm to Load is as simple as changing the gateway you already use to resolve content from Swarm.

* before: [https://api.gateway.ethswarm.org/bzz/](https://api.gateway.ethswarm.org/bzz/)\<hash>
* after: [https://swarm.load.rs/bzz/](https://swarm.wvm.network/bzz/)\<hash>

The first time Load's Swarm gateway sees a new hash, it uploads it to Load Network and serves it directly for subsequent calls. This effectively makes your Swarm data permanent on Load while maintaining the same hash.
