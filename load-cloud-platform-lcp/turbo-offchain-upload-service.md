---
description: Learn about the ARIO's Turbo-compliant offchain upload service
---

# Turbo Offchain Upload Service

### About

The [loaded-turbo-api](https://github.com/loadnetwork/loaded-turbo-api) is  the first [Turbo](https://turbo.ar.io/)-compliant, offchain, s3-based, on HyperBEAM upload service. With this new upload service, Turbo and the broader Arweave ecosystem users can start using [Load S3 ](load-s3-layer.md)temporary storage directly via the official Turbo SDK: a simple -one line- endpoint change.

Thanks to the Turbo SDK’s clear open standards, it was possible to develop a layer on top Load S3 that acts as a upload service that inherits the storage features of Load S3, while maintaining interoperability and integrity with Arweave’s ANS-104 data standard. Load’s upload service inherits the [SLA](https://arweave.net/ZKpbKFbF-F-Yu3Dk9_YCY3klp20PNgBa7JGFtqTYeWs) offered by the Load S3 client.

{% hint style="warning" %}
The upload service limits and features are subject to change, to stay up to date, always check the service’s public configs [here](https://loaded-turbo-api.load.network/) and follow the docs on [GitHub](https://github.com/loadnetwork/loaded-turbo-api).
{% endhint %}

### Roadmap

|                    Endpoint                    | Status |
| :--------------------------------------------: | :----: |
|     `POST /v1/tx/{token}` (<= 10MB uploads)    |    ✅   |
|         `GET /v1/chunks/{token}/-1/-1`         |    -   |
| `POST /v1/chunks/{token}/{upload_id}/{offset}` |    -   |
| `POST /v1/chunks/{token}/{upload_id}/finalize` |    -   |
|   `GET /v1/chunks/{token}/{upload_id}/status`  |    -   |

### Endpoints:

* loaded-turbo-api (offchain, Load S3 bundler endpoint): [https://loaded-turbo-api.load.network](https://loaded-turbo-api.load.network/)
* data cache / fast finality index: [https://gateway.s3-node-1.load.network](https://gateway.s3-node-1.load.network/)

### Examples

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

