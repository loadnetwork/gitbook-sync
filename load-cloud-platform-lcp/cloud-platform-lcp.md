---
description: About Load Cloud Platform
---

# Cloud Platform (LCP)

<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

Uploading data onchain shouldn’t be any more difficult than using Google Drive. The reason tools like Google Drive are popular is because they just work and are cheap/free. Their hidden downsides? You don’t own your data, it’s not permanent, and – especially for blockchain projects – it’s not useful for application developers.

Users just want to put their data somewhere and forget about the upkeep. Developers just want a permanent reference to their data that resolves in any environment. Whichever you are, we built [cloud.load.network](http://cloud.load.network/) for you.

The Load Cloud is an all-in-one tool to interact with various Load Network storage interfaces and pipelines: one UI, one API key, various integrations, with web2 UX.

{% hint style="info" %}
Using the API keys generated on cloud.load.network - you can access other features such as load0 and Load S3 storage.
{% endhint %}

It has been a few months since the [first release](https://blog.load.network/load-cloud/). Since the initial alpha, we have been working towards a more complete dashboard for the onchain data center.

This builds towards our vision of the [decentralized AWS](https://blog.load.network/the-onchain-data-center/) – meeting developers where they are, with familiar devex and 1:1 parallels with the web2 tools they already use. The decentralized compute layer is in place via Load’s EVM/AO layers, and storage via the HyperBEAM-powered S3 device.

The V2 release of the Load Cloud Platform (LCP) introduces several, highly-requested features:

* private ANS-104 DataItems
* DataItems as first-class s3 objects
* access controlled DataItems (s3 objects)
* s3@1.0 device upgrades
* native integration of s3@1.0 with the MinIO cluster.

### The foundation: a universal API key system for Load <a href="#the-foundation-a-universal-api-key-system-for-load" id="the-foundation-a-universal-api-key-system-for-load"></a>

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

V2 introduces the concept of Load accounts and load\_acc API keys. A unified auth layer finally enables us to provision scoped access to Load’s HyperBEAM S3 layer and build access control for data.

Under the hood, Load Cloud email login uses [Magic](http://magic.link/) to create an EVM wallet. This wallet’s keys have access to the Load authentication API and generate keys in the dashboard that can read and write from S3.

This approach enables API access to offchain services with a wallet address as the primary identity, leaving the system open in the future for offchain components to handle things like payments and native integration with onchain compute.

### Private, access-controlled ANS-104 DataItems

Private data and access control are 2 of the most requested features we ever got since we started working with the onchain data center roadmap.

_How can I access my data without necessarily encrypting it and storing the encrypted data onchain? How can I gate access to my data?_

#### Private DataItems

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Private ANS-104 Dataitems are possible today through the introduction of private buckets along load\_acc gated JWT tokens. Before private dataitems, all of Load S3 dataitems were public, and stored according to the \`offchain-dataitems\` data protocol. However, as of today, any LCP user can:

* Create a private bucket and select which LCP users (load\_acc api keys) can access the bucket’s objects
* Upload data to the private bucket and have it stored as ANS-104 Dataitems
* Generate expirable pre-signed URLs for the private data sharing
* Control the access to the private bucket’s dataitems by adding/removing load\_acc API keys

Therefore, LCP & Load S3 users can now store DataItems privately in their private S3 buckets and control the access to the s3 objects, with zero cryptography (encryption/decryption) overhead to keep the data private as if it was pushed to Arweave. Access to Load S3 dataitems can be controlled via signed auto-expiring sharing URLs, or by making the data permanently public on Arweave via Load’s Turbo integration.

### Access Control

As mentioned above, LCP V2 allows its users to control the access over the privately stored offchain DataItems. The access is gated by the uploader’s master load\_acc api key - in the next patch release, we will allow the users to add other LCP users via their registered email address.

With this upgrade, private offchain ANS-104 DataItems are now the first-class data format for S3 objects in Load S3’s LCP.

### s3@1.0 HyperBEAM device upgrades <a href="#s310-hyperbeam-device-upgrades" id="s310-hyperbeam-device-upgrades"></a>

In order to make the private offchain DataItems accessible to their rightful whitelisted users, we had to upgrade the [s3@1.0](mailto:s3@1.0) HyperBEAM device’s sidecar and make it possible to:

* Generate presigned URLs (JWT-gated) for private dataitems given private bucket name, dataitem s3 key, a user’s load\_acc and expiry timestamp. The device then generates a presigned URL, by validating the requester’s correct ownership of the bucket and the dataitem.
* Data streaming of the private dataitem’s data field directly from the S3 cluster, without deserializing the ANS-104 dataitem, to the user’s browser, after JWT validation.

These features have been released under s3\_nif v0.3.1 which are live under s3-node-1.load.network. To check the s3 device sidecar’s upgrades, visit the source code [here](https://github.com/loadnetwork/load_hb/tree/s3-node-1/native/s3_nif/src/sidecar).

### load-s3-agent v4

The Load S3 HTTP API and ANS-104 data orchestrator is now in v4 with several necessary features for the functionality of the LCP backend, and Load S3 clients. One of the most tangent features to this blog post, in the agent’s v4 release, is the /upload/private HTTP POST method that lets LCP users to programmatically push raw data to their LCP’s private bucket, auth’d with the load\_acc API keys, and with the final data format as ANS-104 dataitem, that’s prepared and signed by the agent’s wallet.

```bash
echo -n "hello private world" | curl -X POST https://load-s3-agent.load.network/upload/private \
  -H "Authorization: Bearer $load_acc_api_key" \
  -H "x-bucket-name: $bucket_name" \
  -H "x-folder-name": $folder_name" \ 
  -H "x-dataitem-name: $object_friendly_name" \
  -F "file=@-;type=text/plain" \
  -F "content_type=text/plain"
```

For more examples, check out the [S3 agent repository](https://github.com/loadnetwork/load-s3-agent%20)

### Start Using LCP Today

Today you can use the LCP platform to create buckets, folders and temporarily store data privately in object-storage format. The LCP uses Load's S3 HyperBEAM data storage layer for hotcache storage.

[Start using Load Network Cloud Platform today](https://cloud.load.network)
