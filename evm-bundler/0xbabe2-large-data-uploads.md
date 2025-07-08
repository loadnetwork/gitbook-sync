---
description: >-
  Using Load Network's 0xbabe2 transaction format for large data uploads - the
  largest EVM transaction in history
---

# 0xbabe2: Large Data Uploads

### About 0xbabe2 Transaction Format

0xbabe2 is the newest data transaction format from the Bundler data protocol. Also called "Large Bundle," it's a bundle under version `0xbabe2` (address: [0xbabe2dCAf248F2F1214dF2a471D77bC849a2Ce84](https://explorer.wvm.dev/address/0xbabe2dCAf248F2F1214dF2a471D77bC849a2Ce84)) that exceeds the Load Network L1 and `0xbabe1` transaction input size limits, introducing incredibly high size efficiency to data storage on Load Network.

For example, with Alphanet v0.4.0 metrics running at 500 mgas/s, a Large Bundle has a max size of 246 GB. However, to ensure a smooth DevX and optimal finalization period (aka "safe mode"), we have limited the 0xbabe2 transaction input limit to 2GB at the [Bundler SDK ](broken-reference)level. If you want higher limits, you can achieve this by changing a simple constant!

{% hint style="success" %}
If you have 10 hours to spare, make several teas and watch this 1 GB video streamed to you onchain from the Load Network!\
\
0xbabe2 txid: [https://bundler.load.rs/v2/resolve/0x45cfaff6c3a507b1b1e88ef502ce32f93e7f515d9580ea66c340dc69e9d47608](https://bundler.load.rs/v2/resolve/0x45cfaff6c3a507b1b1e88ef502ce32f93e7f515d9580ea66c340dc69e9d47608)
{% endhint %}

### Architecture design TLDR

&#x20;In simple terms, a Large Bundle consists of `n` smaller chunks (standalone bundles) that are sequentially connected tail-to-head and then at the end the Large Bundle is a reference to all the sequentially related chunks, packing all of the chunks IDs in a single 0xbabe2 bundle and sending it to Load Network.

To dive deeper into the architecture design behind 0xbabe2 and how it works, check out the 0xbabe2 section in the [Bundler documentation](https://github.com/weaveVM/bundler?tab=readme-ov-file#architecture-design).

{% hint style="info" %}
with the upcoming Load Network network release (Alphanet v0.5.0) reaching 1 gigagas/s – 0xbabe2 data size limit will double to 492GB, almost 0.5TB EVM transaction.
{% endhint %}

### 0xbabe2 Broadcasting

Broadcasting an 0xbabe2 to Load Network can be done via the Bundler Rust SDK through 2 ways: the normal 0xbabe2 broadcasting (single-wallet single-threaded) or through the multi-wallet multi-threaded method (using SuperAccount).

#### **Single-Threaded Broadcasting**

Uploading data via the single-threaded method is efficient when the data isn't very large; otherwise, it would have very high latency to finish all data chunking then bundle finalization:

```rust
use bundler::utils::core::large_bundle::LargeBundle;

async fn send_large_bundle_single_thread() -> Result<String, Error> {
    let private_key = String::from("");
    let content_type = "text/plain".to_string();
    let data = "~UwU~".repeat(4_000_000).as_bytes().to_vec();
    let large_bundle = LargeBundle::new()
        .data(data)
        .private_key(private_key)
        .content_type(content_type)
        .chunk()
        .build()?
        .propagate_chunks()
        .await?
        .finalize()
        .await?;
    Ok(large_bundle_hash)
}
```



**Multi-Threaded Broadcasting**

Multi-Threaded 0xbabe2 broadcasting is done via a multi-wallet architecture that ensures parallel chunks settlement on Load Network, maximizing the usage of the network's data throughput. To broadcast a bundle using the multi-threaded method, you need to initiate a `SuperAccount` instance and fund the Chunkers:

```rust
use bundler::utils::core::super_account::SuperAccount;
// init SuperAccount instance
let super_account = SuperAccount::new()
    .keystore_path(".bundler_keystores".to_string())
    .pwd("weak-password".to_string()) // keystore pwd
    .funder("private-key".to_string()) // the pk that will fund the chunkers
    .build();
// create chunkers
let _chunkers = super_account.create_chunkers(Some(256)).await.unwrap(); // Some(amount) of chunkers
// fund chunkers (1 tWVM each)
let _fund = super_account.fund_chunkers().await.unwrap(); // will fund each chunker by 1 tWVM
// retrieve chunkers
let loaded_chunkers = super_account.load_chunkers(None).await.unwrap(); // None to load all chunkers
```

A Super Account is a set of wallets created and stored as keystore wallets locally under your chosen directory. In Bundler terminology, each wallet is called a "chunker". Chunkers optimize the DevX of uploading Large Bundle's chunks to LN by allocating each chunk to a chunker (\~4MB per chunker), moving from a single-wallet single-threaded design in data uploads to a multi-wallet multi-threaded design.

```rust
async fn send_large_bundle_multi_thread() -> Result<String, Error> {
    // will fail until a tLOAD funded EOA (pk) is provided, take care about nonce if same wallet is used as in test_send_bundle_with_target
    let private_key =
        String::from("6f142508b4eea641e33cb2a0161221105086a84584c74245ca463a49effea30b");
    let content_type = "text/plain".to_string();
    let data = "~UwU~".repeat(8_000_000).as_bytes().to_vec();
    let super_account = SuperAccount::new()
        .keystore_path(".bundler_keystores".to_string())
        .pwd("test".to_string());
    let large_bundle = LargeBundle::new()
        .data(data)
        .private_key(private_key)
        .content_type(content_type)
        .super_account(super_account)
        .chunk()
        .build()
        .unwrap()
        .super_propagate_chunks()
        .await
        .unwrap()
        .finalize()
        .await
        .unwrap();
    println!("{:?}", large_bundle);
    Ok(large_bundle)
}
```

#### 0xbabe2 Data Retrieval

0xbabe2 transaction data retrieval can be done either using the Rust SDK or the REST API. Using the REST API to resolve (chunk reconstruction until reaching final data) is faster for user usage as it does chunks streaming, resulting in near-instant data usability (e.g., rendering in browser).\


**Rust SDK**

```rust
async fn retrieve_large_bundle() -> Result<Vec<u8>, Error> {
    let large_bundle = LargeBundle::retrieve_chunks_receipts(
        "0xb58684c24828f8a80205345897afa7aba478c23005e128e4cda037de6b9ca6fd".to_string(),
    )
    .await?
    .reconstruct_large_bundle()
    .await?;
    Ok(large_bundle)
}
```

**REST API**

```bash
curl -X GET https://bundler.load.rs/v2/resolve/$0xBABE2_TXID
```

### What you can fit in a 492GB 0xbabe2 transaction

#### Modern LLMs

| Model                            | What Can Fit in one 0xbabe2 transaction      |
| -------------------------------- | -------------------------------------------- |
| Claude 3 Haiku (70B params)      | 3.51 models (16-bit) or 14.06 models (4-bit) |
| Claude 3 Sonnet (175B params)    | 1.41 models (16-bit) or 5.62 models (4-bit)  |
| Claude 3 Opus (350B params)      | 0.70 models (16-bit) or 2.81 models (4-bit)  |
| Claude 3.5 Sonnet (250B params)  | 0.98 models (16-bit) or 3.94 models (4-bit)  |
| Claude 3.7 Sonnet (300B params)  | 0.82 models (16-bit) or 3.28 models (4-bit)  |
| GPT-4o (1500B params est.)       | 0.16 models (16-bit) or 0.66 models (4-bit)  |
| GPT-4 Turbo (1100B params est.)  | 0.22 models (16-bit) or 0.89 models (4-bit)  |
| Llama 3 70B                      | 3.51 models (16-bit) or 14.06 models (4-bit) |
| Llama 3 405B                     | 0.61 models (16-bit) or 2.43 models (4-bit)  |
| Gemini Pro (220B params est.)    | 1.12 models (16-bit) or 4.47 models (4-bit)  |
| Gemini Ultra (750B params est.)  | 0.33 models (16-bit) or 1.31 models (4-bit)  |
| Mistral Large (123B params est.) | 2.00 models (16-bit) or 8.00 models (4-bit)  |

#### Blockchain Data

| Data Type                                    | What Can Fit in one 0xbabe2 transaction |
| -------------------------------------------- | --------------------------------------- |
| Solana's State Snapshot (\~70GB)             | \~7 instances                           |
| Bitcoin Full Ledger (\~625 GB)               | \~78% of the ledger                     |
| Ethereum Full Ledger (\~1250 GB)             | \~40% of the ledger                     |
| Ethereum blobs (\~2.64 GB per day)           | \~186 days worth of blob data           |
| Celestia's max throughput per day (112.5 GB) | 4.37× capacity                          |

#### Media Files

| File Type                          | What Can Fit in one 0xbabe2 transaction |
| ---------------------------------- | --------------------------------------- |
| MP3 Songs (4MB each)               | 123,000 songs                           |
| Full HD Movies (5GB each)          | 98 movies                               |
| 4K Video Footage (2GB per hour)    | 246 hours                               |
| High-Resolution Photos (3MB each)  | 164,000 photos                          |
| Ebooks (5MB each)                  | 100,000 books                           |
| Documents/Presentations (1MB each) | 492,000 files                           |

#### Other Data

| Data Type                            | What Can Fit in one 0xbabe2 transaction |
| ------------------------------------ | --------------------------------------- |
| Database Records (5KB per record)    | 98 billion records                      |
| Virtual Machine Images (8GB each)    | 61 VMs                                  |
| Docker container images (500MB each) | 1,007 containers                        |
| Genome sequences (4GB each)          | 123 genomes                             |
