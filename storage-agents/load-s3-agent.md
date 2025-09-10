---
description: The LCP data agent
---

# Load S3 Agent

The first agent in the list is [load-s3-agent](https://github.com/loadnetwork/load-s3-agent) — an orchestrator agent that makes it possible to interact with both Load S3 and Arweave under a single HTTP endpoint, with a self-managed data bridge from Load S3 to Arweave.

### Agent API

* GET `/` : agent info
* GET `/stats` : storage stats
* GET `/:dataitem_id` : generate a presigned get\_object URL to access the ANS-104 DataItem data.
* POST `/upload` : post data to store a DataItem offchain on `~s3@1.0`
* POST `/post/:dataitem_id` : post an `~s3@1.0` DataItem to Arweave via Turbo (N.B: Turbo covers any dataitem cost with size <= 100KB).

#### Upload data and return an agent signed DataItem

```bash
echo -n "hello world" | curl -X POST https://load-s3-agent.load.network/upload \
  -H "Authorization: Bearer REACH_OUT_TO_US" \
  -F "file=@-;type=text/plain" \
  -F "content_type=text/plain"
```

#### Upload a signed DataItem and store it in Load S3

```bash
curl -X POST https://load-s3-agent.load.network/upload \
  -H "Authorization: Bearer REACH_OUT_TO_US" \
  -H "signed: true" \
  -F "file=@your-signed-dataitem.bin"
```

#### Post offchain DataItem to Arweave

for offchain dataitem `eoNAO-HlYasHJt3QFDuRrMVdLUxq5B8bXe4N_kboNWs`

```bash
curl -X POST \
  "https://load-s3-agent.load.network/post/eoNAO-HlYasHJt3QFDuRrMVdLUxq5B8bXe4N_kboNWs" \
  -H "Authorization: Bearer REACH_OUT_TO_US" \
  -H "Content-Type: application/json"
```

