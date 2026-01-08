---
description: The LCP data agent
---

# Load S3 Agent (HTTP API)

### About

`load-s3-agent` is a data agent built on top of HyperBEAM `~s3@1.0` temporal data storage device. This agent orchestrates the location of the data moving it from temporal to permanent (Arweave).

> N.B: beta testing release, unstable and subject to breaking changes, use in testing enviroments only.

### Agent API

* GET `/` : agent info
* GET `/provenance/{id}` : return the onchain provenance proof of an offchain ANS-104 dataitem (only for dataitems that enabled the onchain provenance flag at upload).
* GET `/:dataitem_id` : generate a presigned get\_object URL to access the ANS-104 DataItem data - **DEPRECATED since v0.7.0** - use `gateway.s3-node-1.load.network/resolve/$DATAITEM_ID` instead
* GET `/agg/daily?days=N` : return the sum-per-day new added data size and dataitems count. N default to 30.
* GET `/agg/daily/mime` : return the aggregated content-type frequency per day.
* GET `/agg/daily/anchored` : return the aggregated count of anchored LS3 dataitems to Arweave.
* POST `/tags/query` : query dataitems for a given tags KV pairs.
* POST `/query/dataitem` : fetch full metadata for a specific `dataitem_id`.
* POST `/upload` : post data (or signed dataitem) to store a public offchain DataItem on `~s3@1.0`
* POST `/upload/private` : post data (or signed dataitem) to store a private offchain DataItem on `~s3@1.0`
* POST `/post/:dataitem_id` : post an `~s3@1.0` public DataItem to Arweave via Turbo (N.B: Turbo covers any dataitem cost with size <= 100KB).

#### Upload data and return an agent public signed DataItem

```bash
echo -n "hello world" | curl -X POST https://load-s3-agent.load.network/upload \
  -H "Authorization: Bearer $load_acc_api_key" \
  -F "file=@-;type=text/plain" \
  -F "content_type=text/plain"
```

Or optionally add custom tags KVs that will be included in the ANS-104 DataItem construction

```bash
echo -n "hello custom tagged world"  | curl -X POST https://load-s3-agent.load.network/upload \
    -H "Authorization: Bearer $load_acc_api_key" \
    -F "file=@-;type=text/plain" \
    -F 'tags=[{"key":"tag1","value":"tag1"},{"key":"tag2","value":"tag2"}]'
```

Optional: have the agent publish an offchain provenance record for the uploaded DataItem (the API returns the provenance transaction id in `offchain_provenance_proof`):

```bash
echo -n "hello provenance world" | curl -X POST https://load-s3-agent.load.network/upload \
  -H "Authorization: Bearer $load_acc_api_key" \
  -H "offchain-provenance: true" \
  -F "file=@-;type=text/plain"
```

offchain provenance data protocol tags on Arweave (per provenance proof):

* `Storage-Provider: Load-S3`
* `Agent-Version: ${VERSION}`
* `Dataitem-Record: ${OFFCHAIN_LS3_DATAITEM_ID}`
* `Version:$PROVENANCE_PROTOCOL_VERSION` &#x20;
* `Data-Protocol: Load-S3-Offchain-Provenance`

Examples:

* onchain provenance example: https://viewblock.io/arweave/tx/fnmmd6pqOOQFFpscH8AB4CyGDsGSt\_fG5VCPJioBG3c &#x20;
* offchain dataitem: https://gateway.s3-node-1.load.network/resolve/gT03XTtL0GCfb2AxJFjg8OVkhMuLQvwQVi7KvrbKYF8
* provenance receipt (including settlement): https://load-s3-agent.load.network/provenance/gT03XTtL0GCfb2AxJFjg8OVkhMuLQvwQVi7KvrbKYF8

#### Upload data and return an agent private signed DataItem

\*\*\* N.B: any private DataItem does not have the tags indexed nor is queryable \*\*\*

```bash
echo -n "hello world" | curl -X POST https://load-s3-agent.load.network/upload/private \
  -H "Authorization: Bearer $load_acc_api_key" \
  -H "x-bucket-name: $bucket_name" \
  -H "x-dataitem-name: $dataitem_name" \
  -H "x-folder-name": $folder_name" \ 
  -H "signed: false" \  
  -F "file=@-;type=text/plain" \
  -F "content_type=text/plain"
```

#### Upload signed dataitem to a private bucket (private dataitem)

```bash
curl -X POST https://load-s3-agent.load.network/upload/private \
  -H "Authorization: Bearer $load_acc_api_key" \
  -H "signed: true" \
  -H "bucket_name: $bucket_name" \
  -H "x-dataitem-name: $dataitem_name" \
  -H "x-folder-name": $folder_name" \ 
  -F "file=@path-signed-dataitem.ans104" \
  -F "content_type=application/octet-stream"
```

#### Upload a signed DataItem and store it in Load S3

Tags are extracted from the ANS-104 DataItem, indexed and queryable

```bash
curl -X POST https://load-s3-agent.load.network/upload \
  -H "Authorization: Bearer $load_acc_api_key" \
  -H "signed: true" \
  -F "file=@your-signed-dataitem.bin"
```

#### Post offchain DataItem to Arweave

example: for offchain dataitem = `eoNAO-HlYasHJt3QFDuRrMVdLUxq5B8bXe4N_kboNWs`

```bash
curl -X POST \
  "https://load-s3-agent.load.network/post/eoNAO-HlYasHJt3QFDuRrMVdLUxq5B8bXe4N_kboNWs" \
  -H "Authorization: Bearer REACH_OUT_TO_US" \
  -H "Content-Type: application/json"
```

#### Querying DataItems by Tags

all dataitems pushed after agent's `v0.6.0` release are queryable by the dataitem's tags KVs:

```bash
curl -X POST https://load-s3-agent.load.network/tags/query \
  -H "Content-Type: application/json" \
  -d '{
    "filters": [
      {
        "key": "tag1",
        "value": "tag1"
      },
      {
        "key": "tag2",
        "value": "tag2"
      }
    ]
  }'
```

* Pagination follows Arweave's GQL schema: optional `first` (default 25, max 100) and a cursor `after`.
* `full_metadata` flag (`Optional<bool>`) to return the full tags associated with a query's dataitem
* `owner` / `target` filters are supported (can be used with or without `filters`)
* Tip: `owner` / `target` query support is available from agent `v0.10.0`
* each returned item includes `dataitem_size` (bytes) when available (v0.9.1 and onwards)
* `created_after` / `created_before` (ISO-8601/RFC3339 strings) filter items by their `created_at` timestamp (inclusive bounds).

```bash
curl -X POST https://load-s3-agent.load.network/tags/query \
  -H "Content-Type: application/json" \
  -d '{
    "filters": [
      {
        "key": "tag1",
        "value": "tag1"
      }
    ],
    "full_metadata": true,
    "owner": "2BBwe2pSXn_Tp-q_mHry0Obp88dc7L-eDIWx0_BUfD0",
    "target": null,
    "created_after": "2025-12-01T00:00:00Z",
    "created_before": "2025-12-05T00:00:00Z",
    "first": 25,
    "after": null
  }'
```

Owner-only query:

```bash
curl -X POST https://load-s3-agent.load.network/tags/query \
  -H "Content-Type: application/json" \
  -d '{
    "filters": [],
    "owner": "2BBwe2pSXn_Tp-q_mHry0Obp88dc7L-eDIWx0_BUfD0"
  }'
```

Owner + tags query:

```bash
curl -X POST https://load-s3-agent.load.network/tags/query \
  -H "Content-Type: application/json" \
  -d '{
    "filters": [
      {
        "key": "Content-Type",
        "value": "text/plain"
      }
    ],
    "owner": "2BBwe2pSXn_Tp-q_mHry0Obp88dc7L-eDIWx0_BUfD0",
    "full_metadata": true
  }'
```

if `page_info.has_next_page` returns true, reuse the `page_info.next_cursor` string as the next `after`.

`count` reflects the number of items returned in the current page, while `total_count` includes every dataitem that matches the filters across all pages.

#### Querying a DataItem by ID

Retrieve the latest indexed metadata (content type, size, tags, timestamps) for a single dataitem:

```bash
curl -X POST https://load-s3-agent.load.network/query/dataitem \
  -H "Content-Type: application/json" \
  -d '{
    "dataitem_id": "lIBThW1eowNfBTotAY2cTIIaqtZggtJRQTdHF_14co8"
  }'
```

#### Associating data with a pool

[Pools](https://data.load.network/pools) are an experimental feature which will allow crowdfunded settlement of temporary DataItems to Arweave using [xANS-104 paradigm](https://blog.load.network/xans-104/). To associate data with a pool when uploading it, set a `Data-Protocol` tag:

```bash
echo -n "hello custom tagged world" | curl -X POST https://load-s3-agent.load.network/upload
-H "Authorization: Bearer $load_acc_api_key"
-F "file=@-;type=text/plain"
-F 'tags=[{"key":"Data-Protocol","value":"MyCustomValue"}]'
```

Items with protocol tags will be displayed in the pools explorer when the feature goes into public beta.

### Load S3 Agent library

**Adding load-s3-agent library**

```toml
[dependencies]
load-s3-agent = { git = "https://github.com/loadnetwork/lcp-uploader-api.git", branch = "main" }
```

**Example**

Codec helpers (owner/target derivation) are available under `load_s3_agent::codec`.

```rust
use load_s3_agent::create_dataitem;
use load_s3_agent::{get_dataitem_provenance_record, DataItemProvenanceRecord};

fn build_item() -> anyhow::Result<()> {
    // ensure UPLOADER_JWK is available in the environment (or .env)
    std::env::set_var("UPLOADER_JWK", include_str!("../wallet.json"));

    let item = create_dataitem(
        b"hello world".to_vec(),
        "text/plain",
        &[("My-Tag".into(), "tag-value".into())],
    )?;

    println!("Signed data item id: {}", item.id());
    Ok(())
}
```

```rust
use load_s3_agent::{codec::derive_owner_address, create_dataitem};

fn owner_from_item() -> anyhow::Result<()> {
    std::env::set_var("UPLOADER_JWK", include_str!("../wallet.json"));
    let item = create_dataitem(b"hello world".to_vec(), "text/plain", &[])?;
    let owner = derive_owner_address(&item).unwrap_or_default();
    println!("owner: {owner}");
    Ok(())
}
```
