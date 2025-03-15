---
description: About LN-ExEx Data Protocol on Arweave
---

# LN-ExEx Data Protocol

### About

The `LN-ExEx` data protocol on Arweave is responsible for archiving Load Network's full block data, which is posted to Arweave using the [Arweave Data Uploader Execution Extension (ExEx).](../load-network-exex/load-network-exexes/arweave-data-uploader.md)&#x20;

{% hint style="warning" %}
After the rebrand from WeaveVM to Load Network, all the data protocol tags have changed the "\*WeaveVM\*" onchain term (Arweave tag) to "\*LN\*"
{% endhint %}

### Protocol Specifications

The data protocol transactions follow the ANS-104 data item specifications. Each Load Network block is posted on Arweave, after borsh-brotli encoding, with the following tags:

| Tag Name         | Tag Value                  | Description                                                                                          |
| ---------------- | -------------------------- | ---------------------------------------------------------------------------------------------------- |
| `Protocol`       | `LN-ExEx`                  | Data protocol identifier                                                                             |
| `ExEx-Type`      | `Arweave-Data-Uploader`    | The Load Network ExEx type                                                                           |
| `Content-Type`   | `application/octet-stream` | Arweave data transaction MIME type                                                                   |
| `LN:Encoding`    | `Borsh-Brotli`             | Transaction's data encoding algorithms                                                               |
| `Block-Number`   | `$value`                   | Load Network block number                                                                            |
| `Block-Hash`     | `$value`                   | Load Network block hash                                                                              |
| `Client-Version` | `$value`                   | Load Network Reth client version                                                                     |
| `Network`        | `Alphanet vx.x.x`          | Load Network Alphanet semver                                                                         |
| `LN:Backfill`    | `$value`                   | Boolean, if the data has been posted by a backfiller (true) or archiver (false or not existing data) |

### LN-ExEx Data Items Uploaders

* Reth ExEx Archiver Address: [5JUE58yemNynRDeQDyVECKbGVCQbnX7unPrBRqCPVn5Z](https://viewblock.io/arweave/address/5JUE58yemNynRDeQDyVECKbGVCQbnX7unPrBRqCPVn5Z?tab=items)
* Arweave-ExEx-Backfill Address: [F8XVrMQzsHiWfn1CaKtUPxAgUkATXQjXULWw3oVXCiFV](https://viewblock.io/arweave/address/F8XVrMQzsHiWfn1CaKtUPxAgUkATXQjXULWw3oVXCiFV?tab=items)
