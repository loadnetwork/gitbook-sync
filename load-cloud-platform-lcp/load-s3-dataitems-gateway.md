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

* preview: [https://gateway.s3-node-1.load.network/resolve/preview/c4LcmsDJgJV\_aUd8jrrtmeYoDkb6Xzk13p67ibfpyrA](https://gateway.s3-node-1.load.network/resolve/preview/c4LcmsDJgJV_aUd8jrrtmeYoDkb6Xzk13p67ibfpyrA)
* manifest json file: [https://gateway.s3-node-1.load.network/resolve/c4LcmsDJgJV\_aUd8jrrtmeYoDkb6Xzk13p67ibfpyrA](https://gateway.s3-node-1.load.network/resolve/c4LcmsDJgJV_aUd8jrrtmeYoDkb6Xzk13p67ibfpyrA)



Source code: [https://github.com/loadnetwork/load\_hb/tree/s3-node-1/native/s3\_nif/src/sidecar](https://github.com/loadnetwork/load_hb/tree/s3-node-1/native/s3_nif/src/sidecar)&#x20;
