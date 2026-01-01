---
description: Accessing the Load S3 data via HyperBEAM gateways
---

# Load S3 DataItems Gateway

The easiest way to access Load S3 stored dataitems is the HyperBEAM gateway sidecar, that works over HTTP — endpoint: https://gateway.s3-node-1.load.network&#x20;

### Gateway Methods

#### Resolving public dataitem data

`GET /resolve/{dataitem_key}`

#### Downloading full dataitem object (.ans104 binary)

`GET /binary/{dataitem_key}`

For entreprise Load S3 clients, the methods above are both available under the gateway, but for the load s3-fast cluster

* `GET /resolve/fast/{dataitem_key}`
* `GET /binary/fast/{dataitem_key}`&#x20;

### Resolving Arweave Manifests (Preview)

For web apps deployed as offchain LS3 dataitems (`arweave/paths` manifest), for example using the `permaweb-deploy` cli, the LS3 gateway can properly resolve the manifests under the `/resolve/preview/{manifest_dataitem_id}` endpoint; example:&#x20;

* preview: [https://gateway.s3-node-1.load.network/resolve/preview/n8nMf7oSQgBZDEw1mCsGslsp\_zAZwmCf4AgTFHSC0C0](https://gateway.s3-node-1.load.network/resolve/preview/n8nMf7oSQgBZDEw1mCsGslsp_zAZwmCf4AgTFHSC0C0)&#x20;
* manifest json file: [https://gateway.s3-node-1.load.network/resolve/n8nMf7oSQgBZDEw1mCsGslsp\_zAZwmCf4AgTFHSC0C0](https://gateway.s3-node-1.load.network/resolve/n8nMf7oSQgBZDEw1mCsGslsp_zAZwmCf4AgTFHSC0C0)

to resolve prod (Arweave settled) manifests, use the `/resolve/prod/{manifest_dataitem_id}` endpoint - this endpoint resolve anchored LS3 manifests as well as Arweave-first manifests:&#x20;

* [https://gateway.s3-node-1.load.network/resolve/prod/rquIQPiahInYInWyMACxEK4EU27VAt3FZXFPdPYI6vo](https://gateway.s3-node-1.load.network/resolve/prod/rquIQPiahInYInWyMACxEK4EU27VAt3FZXFPdPYI6vo)



Source code: [https://github.com/loadnetwork/load\_hb/tree/s3-node-1/native/s3\_nif/src/sidecar](https://github.com/loadnetwork/load_hb/tree/s3-node-1/native/s3_nif/src/sidecar)&#x20;
