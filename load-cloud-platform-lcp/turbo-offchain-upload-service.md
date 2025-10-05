---
description: Learn about the ARIO's Turbo-compliant offchain upload service
---

# Turbo Offchain Upload Service

### About

The [loaded-turbo-api](https://github.com/loadnetwork/loaded-turbo-api) is  the first [Turbo](https://turbo.ar.io/)-compliant, offchain, s3-based, on HyperBEAM upload service. With this new upload service, Turbo and the broader Arweave ecosystem users can start using [Load S3 ](load-s3-layer-ls3.md)temporary storage directly via the official Turbo SDK: a simple -one line- endpoint change.

Thanks to the Turbo SDK’s clear open standards, it was possible to develop a layer on top Load S3 that acts as a upload service that inherits the storage features of Load S3, while maintaining interoperability and integrity with Arweave’s ANS-104 data standard. Load’s upload service inherits the [SLA](https://arweave.net/ZKpbKFbF-F-Yu3Dk9_YCY3klp20PNgBa7JGFtqTYeWs) offered by the Load S3 client.

{% hint style="warning" %}
The upload service limits and features are subject to change, to stay up to date, always check the service’s public configs [here](https://loaded-turbo-api.load.network/) and follow the docs on [GitHub](https://github.com/loadnetwork/loaded-turbo-api).
{% endhint %}

### Compatibility

|                    Endpoint                    |                                                                Status                                                               |
| :--------------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------: |
|               `GET /` `GET /info`              |                                                                  ✅                                                                  |
|             `GET /bundler_metrics`             |                                                           ✅ (placeholder)                                                           |
|                  `GET /health`                 |                                                                  ✅                                                                  |
|       `GET /v1/tx/{dataitem_id}/offsets`       |                                                           ✅ (placeholder)                                                           |
|     `POST /v1/tx/{token}` (<= 10MB uploads)    |                                                                  ✅                                                                  |
|        `GET /v1/tx/{dataitem_id}/status`       |                                                                  ✅                                                                  |
|         `GET /v1/chunks/{token}/-1/-1`         |                                                                  ✅                                                                  |
|     `GET /v1/chunks/{token}/{upload_id}/-1`    |                                                                  ✅                                                                  |
|   `GET /v1/chunks/{token}/{upload_id}/status`  |                                                                  ✅                                                                  |
| `POST /v1/chunks/{token}/{upload_id}/{offset}` |                                                                  ✅                                                                  |
| `POST /v1/chunks/{token}/{upload_id}/finalize` |                                                                  ✅                                                                  |
|           `GET /account/balance/:id`           | not supported, [deprecated](https://github.com/ardriveapp/turbo-upload-service/blob/main/src/router.ts#L48) in turbo-upload-service |
|         `GET /price/:token/:byteCount?`        |                                          not supported, deprecated in turbo-upload-service                                          |

### Service Limits & Specs

The offchain LS3-powered ANS-104 upload service match the services standards set by the Turbo service, such as:

* 4GB dataitem max size
* return service-signed receipts
* match the multipart upload biz logic, including chunking strategy (min/max/default chunk size)
* it does not bundle dataitems as they are stored on LS3, it leaves the bundling logic for the onchain upload service that requires onchain fees fine tuning (e.g. Turbo)
* comes along fast finality indexes & data caches, a LS3 dataitem streaming gateway powered by HyperBEAM: [https://gateway.s3-node-1.load.network](https://gateway.s3-node-1.load.network/)
* service AR address: `2BBwe2pSXn_Tp-q_mHry0Obp88dc7L-eDIWx0_BUfD0`
* offchain -> onchain dataitems anchoring is supported via the [load-s3-agent](https://github.com/loadnetwork/load-s3-agent?tab=readme-ov-file#post-offchain-dataitem-to-arweave)

### Endpoints:

* loaded-turbo-api (offchain, Load S3 bundler endpoint): [https://loaded-turbo-api.load.network](https://loaded-turbo-api.load.network/)
* data cache / fast finality index: [https://gateway.s3-node-1.load.network](https://gateway.s3-node-1.load.network/)

### Examples

#### Small uploads (<= 10 MB)

```js
import {
  TurboFactory,
  developmentTurboConfiguration,
} from '@ardrive/turbo-sdk/node';
import Arweave from 'arweave';
import fs from 'fs';

(async () => {
  // Create a test file
  const testData = 'Hello from loaded-turbo-api S3 bundler!';
  fs.writeFileSync('test-file.txt', testData);

  // Create an Arweave key for testing
  const arweave = new Arweave({});
  const jwk = await Arweave.crypto.generateJWK();
  const address = await arweave.wallets.jwkToAddress(jwk);
  console.log('Test wallet address:', address);

  const customTurboConfig = {
    ...developmentTurboConfiguration,
    uploadServiceConfig: {
      url: 'https://loaded-turbo-api.load.network', // loaded-turbo-api endpoint
    },
  };

  // Create authenticated client
  const turboAuthClient = TurboFactory.authenticated({
    privateKey: jwk,
    ...customTurboConfig,
  });

  try {
    // Test upload
    console.log('Uploading file loaded-turbo-api');
    const uploadResult = await turboAuthClient.uploadFile({
    fileStreamFactory: () => fs.createReadStream('test-file.txt'),
    fileSizeFactory: () => fs.statSync('test-file.txt').size,
    dataItemOpts: {
        tags: [
        { name: 'Content-Type', value: 'text/plain' }
        ]
    },
    signal: AbortSignal.timeout(30_000),
    });


    console.log('Upload successful!');
    console.log(JSON.stringify(uploadResult, null, 2));

    // Verify the response structure
    console.log('\n=== Response Validation ===');
    console.log('ID:', uploadResult.id);
    console.log('Owner:', uploadResult.owner);
    console.log('Winc:', uploadResult.winc);
    console.log('Data Caches:', uploadResult.dataCaches);
    console.log('Fast Finality Indexes:', uploadResult.fastFinalityIndexes);

  } catch (error) {
    console.error('Upload failed:', error);
    if (error.response) {
      console.error('Response status:', error.response.status);
      console.error('Response data:', error.response.data);
    }
  } finally {
    fs.unlinkSync('test-file.txt');
  }
})();
```

Example offchain uploaded DataItem fast indexer access: [https://gateway.s3-node-1.load.network/resolve/Y11-TiVivfQpcg7eDV8ouxJfQl2UHvFFPgs-NJ\_HC2k](https://gateway.s3-node-1.load.network/resolve/Y11-TiVivfQpcg7eDV8ouxJfQl2UHvFFPgs-NJ_HC2k)&#x20;

#### Large multipart resumable uploads

```js
import {
  TurboFactory,
  developmentTurboConfiguration,
} from '@ardrive/turbo-sdk/node';
import Arweave from 'arweave';
import fs from 'fs';

(async () => {
  // Create large file to force multipart upload (>10MB)
  const largeTestData = 'A'.repeat(15 * 1024 * 1024); // 15MB
  fs.writeFileSync('large-test-file.txt', largeTestData);
  console.log('Created 15MB test file');

  // Generate test wallet
  const arweave = new Arweave({});
  const jwk = await Arweave.crypto.generateJWK();
  const address = await arweave.wallets.jwkToAddress(jwk);
  console.log('Test wallet address:', address);

  const customTurboConfig = {
    ...developmentTurboConfiguration,
    uploadServiceConfig: {
      url: 'https://loaded-turbo-api.load.network',
    },
  };

  const turboAuthClient = TurboFactory.authenticated({
    privateKey: jwk,
    ...customTurboConfig,
  });

  try {
    console.log('Starting multipart upload...');
    
    const uploadResult = await turboAuthClient.uploadFile({
      fileStreamFactory: () => fs.createReadStream('large-test-file.txt'),
      fileSizeFactory: () => fs.statSync('large-test-file.txt').size,
      dataItemOpts: {
        tags: [{ name: 'Content-Type', value: 'text/plain' }]
      },
      events: {
        onProgress: ({ totalBytes, processedBytes, step }) => {
          const percent = ((processedBytes / totalBytes) * 100).toFixed(1);
          console.log(`${step.toUpperCase()} Progress: ${percent}% (${processedBytes}/${totalBytes})`);
        },
        onUploadProgress: ({ totalBytes, processedBytes }) => {
          console.log(`Upload: ${((processedBytes / totalBytes) * 100).toFixed(1)}%`);
        },
      },
    });

    console.log('Result:', uploadResult);

  } catch (error) {
    console.error('\nUpload failed:', error.message);
    if (error.response) {
      console.error('Status:', error.response.status);
      console.error('Data:', error.response.data);
    }
  } finally {
    fs.unlinkSync('large-test-file.txt');
  }
})();

```

Example signed receipt: [https://loaded-turbo-api.load.network/v1/chunks/arweave/541a7043-6706-47e3-be73-907bffb17a80/status](https://loaded-turbo-api.load.network/v1/chunks/arweave/541a7043-6706-47e3-be73-907bffb17a80/status)&#x20;

Check out the upload service v1.0.0 release [here](https://github.com/loadnetwork/loaded-turbo-api/releases/tag/v1.0.0)&#x20;

### Uploading data using AWS S3 SDK / Load Agent vs Turbo SDK

This Turbo-compliant upload service, along the load-s3-agent, form the main 2 data objects ingress as ANS-104 DataItems.

The main difference between load-s3-agent and Turbo-SDK is access control. Uploading data via Turbo SDK default to the `offchain-dataitems` data protocol where all uploaded data items sit in the protocol’s public bucket, while using the load s3 agent, it’s possible to upload object -dataitems- to [private buckets](https://github.com/loadnetwork/load-s3-agent?tab=readme-ov-file#upload-data-and-return-an-agent-private-signed-dataitem) and control the access and have expireable shareable download links.

However in both cases, the outcome is equal: ANS-104 formatted S3 objects along its data provenance guarantees and Arweave alignment.
