---
description: About the Data Protocol of Load Network Precompile Contracts
---

# Load Network Precompiles Data Protocol

### About

Load Network have precompiled contracts that push data directly to Arweave as ANS-104 data items. One such precompile is the [`0x17`](https://docs.wvm.dev/using-weavevm/weavevm-precompiles#id-1-precompile-0x17-upload-data-from-solidity-to-arweave) precompile (`arweave_upload)`.

{% hint style="warning" %}
After the rebrand from WeaveVM to Load Network, all the data protocol tags have changed the "\*WeaveVM\*" onchain term (Arweave tag) to "\*LN\*"
{% endhint %}



#### Protocol Specifications <a href="#protocol-specifications" id="protocol-specifications"></a>

The data protocol transactions follow the ANS-104 data item specifications. Each LN precompile transaction is posted on Arweave, after brotli compression, with the following tags:

| Tag Name                | Tag Value                  | Description                                                        |
| ----------------------- | -------------------------- | ------------------------------------------------------------------ |
| `LN:Precompile`         | `true`                     | Data protocol identifier                                           |
| `Content-Type`          | `application/octet-stream` | Arweave data transaction MIME type                                 |
| `LN:Encoding`           | `Brotli`                   | Transaction's data encoding algorithms                             |
| `LN:Precompile-Address` | `$value`                   | The decimal precompile number (e.g. 0x17 have the Tag Value of 23) |

#### Load Network Precompile Data Items Uploaders <a href="#weavevm-exex-data-items-uploaders" id="weavevm-exex-data-items-uploaders"></a>

* Load Network Reth Precompiles Address: [5JUE58yemNynRDeQDyVECKbGVCQbnX7unPrBRqCPVn5Z](https://viewblock.io/arweave/address/5JUE58yemNynRDeQDyVECKbGVCQbnX7unPrBRqCPVn5Z?tab=items)
