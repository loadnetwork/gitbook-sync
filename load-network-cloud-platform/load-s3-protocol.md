---
description: Migrate to a permanent S3-compatible object storage in a single line change
---

# Load S3 Protocol

### About

Load.Network provides an S3 implementation which enables developers to store files permanently in a decentralized manner by using the common AWS S3 Patterns with minimal change.

#### Data pipelines

At the time of writing, the Load S3 supports two data pipelines:

* load0: programmable EVM data with the Arweave permanence
* Turbo Arweave: Arweave ans-104 dataitems

In the near future, we will add a third data provider, Load S3 HyperBEAM, facilitating temporal data storage 7.7x cheaper than AWS S3.

### Installation

Load.Network is compatible with the S3 SDKs, because of this, you are able to use existing libraries.

#### NodeJS

To install the official S3 library in NodeJS, run the following command

```shell
$ yarn add @aws-sdk/client-s3
```

**Initialization**

In order to initialize the S3 client connected to Load Network, you can do the following:

```typescript
import { S3Client } from "@aws-sdk/client-s3";

const accessKeyId = process.env.LOAD_ACCESS_KEY;
const secretAccessKey = ""; // It's meant to be empty

const s3Client = new S3Client({
    region: "eu-west-2", // Required -- current supported region
    endpoint: "https://s3.load.rs", // Load.Network S3 endpoint
    credentials: {
        accessKeyId,
        secretAccessKey,
    },
    forcePathStyle: true, // Required
});
```

* `process.env.LOAD_ACCESS_KEY`: Contains your private service key in [cloud.load.network](https://cloud.load.network).
  * It looks similar to `load_acc_*******`
* `https://s3.load.rs` is the endpoint for the S3 interface provided by Load -- [codebase](https://github.com/weaveVM/wvm-aws-sdk-s3)
* `forcePathStyle` set to `true` is _always_ necessary.
* (optional) use `"uploader-api": "turbo"` is the command metadata object to push the object data to Arweave via Turbo. Default is load0.

#### JS Example for PutObjectCommandArweave

```javascript
import { S3Client, PutObjectCommand } from "@aws-sdk/client-s3";

async function uploadFile(bucketName) {
  const key = "filename.turbo"; // The name (key) the file will be stored under (object name)
  const fileContent = new Uint8Array([0, 1, 2, 3, 4]); // Example file content in bytes
  const accessKeyId = process.env.LOAD_ACCESS_KEY;
  const secretAccessKey = ""; /// meant to be empty

  const s3Client = new S3Client({
    region: "eu-west-2",
    endpoint: "https://s3.load.rs",
    credentials: {
      accessKeyId: accessKeyId,
      secretAccessKey: secretAccessKey,
    },
    forcePathStyle: true,
  });

  try {
    const command = new PutObjectCommand({
      Bucket: bucketName,
      Key: key,
      Body: fileContent,
      Metadata: {
        "uploader-api": "turbo", // to push object to Arweave
      },
    });

    const result = await s3Client.send(command);
    console.log("✅ File uploaded:", key);
    console.log(result);
    return result;
  } catch (error) {
    console.error("❌ Error uploading file:", error);
  }
}

uploadFile("your-bucket-name");
```

### Rust Examples

```rust
use aws_sdk_s3::error::SdkError;
use aws_sdk_s3::operation::create_bucket::CreateBucketError;
use aws_sdk_s3::Client;

pub async fn create_client() -> Client {
    let config = aws_config::from_env()
        .endpoint_url("https://s3.load.rs")
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

for more examples, checkout the [rust-examples](https://github.com/loadnetwork/s3-examples/tree/main/rust-examples).

Github repo: [https://github.com/weaveVM/wvm-aws-sdk-s3](https://github.com/weaveVM/wvm-aws-sdk-s3)&#x20;

For more code examples, checkout this repository: [https://github.com/loadnetwork/s3-examples](https://github.com/loadnetwork/s3-examples)
