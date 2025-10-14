---
description: Learn how to use Load S3 storage layer along LCP's load_acc api keys
---

# LS3 with load\_acc

As of October 1st, 2025, it's no longer required to directly contact us to acquire Load S3 access keys to interact with the LS3's `drive.load.network` HyperBEAM cluster. Interacting with the Load S3 storage layer now has programmatic access with `load_acc` access keys and a new cluster endpoint: `https://api.load.network/s3`&#x20;

### How to get load\_acc API keys

First, you have to create a bucket from the cloud.load.network dashboard if the bucket you want to create scoped keys for does not already exist.

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

After creating the bucket, navigate to the "API Keys" tab and create a new load\_acc key with a label, then scope it to the desired bucket.

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

### load\_acc in action

After creating a bucket and load\_acc API key, you can now interact with the Load S3 storage layer via S3-compatible SDKs, such as the official AWS S3 SDK. Here is the S3 client configuration as it should be:

```javascript
import { S3Client } from "@aws-sdk/client-s3";


const endpoint = "https://api.load.network/s3"; // new endpoint, not drive.load.network 
const accessKeyId = "load_acc_YOUR_LCP_ACCESS_KEY";
const secretAccessKey = ""; 

// Initialize the S3 client
const s3Client = new S3Client({
  region: "eu-west-2",
  endpoint,
  credentials: {
    accessKeyId,
    secretAccessKey,
  },
  forcePathStyle: true, // required
});
```

And that's it, that's all you need to start interacting with your HyperBEAM-powered S3 temporary storage!
