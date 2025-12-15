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

* `GET /resolve/{dataitem_key}`
* `GET /binary/fast/{dataitem_key}`&#x20;

Source code: [https://github.com/loadnetwork/load\_hb/tree/s3-node-1/native/s3\_nif/src/sidecar](https://github.com/loadnetwork/load_hb/tree/s3-node-1/native/s3_nif/src/sidecar)&#x20;
