---
description: About Load Network precompiled contracts
---

# Load Network Precompiles

### What Are Precompiled Contracts?

Ethereum uses precompiles to efficiently implement cryptographic primitives within the EVM instead of re-implementing these primitives in Solidity.&#x20;

The following precompiles are currently included: ecrecover, sha256, blake2f, ripemd-160, Bn256Add, Bn256Mul, Bn256Pairing, the identity function, modular exponentiation, and point evaluation.

Ethereum precompiles behave like smart contracts built into the Ethereum protocol. The ten precompiles live in addresses 0x01 to 0x0A. Load Network supports all of these 10 standard precompiles and adds new custom precompiles starting at the 23rd byte representing the letter "W" position (index) in the alphabet. Therefore, Load Network precompiles start at address 0x17 (23rd byte).

### Load Network Precompiles List

| Address                                             | Name                       | Minimum Gas | Input                        | Output        | Description                                                                                                                                                              |
| --------------------------------------------------- | -------------------------- | ----------- | ---------------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 0x01 (`0x0000000000000000000000000000000000000001`) | ecRecover                  | 3000        | hash, v, r, s                | publicAddress | Elliptic curve digital signature algorithm (ECDSA) public key recovery function                                                                                          |
| 0x02 (`0x0000000000000000000000000000000000000002`) | SHA2-256                   | 60          | data                         | hash          | Hash function                                                                                                                                                            |
| 0x03 (`0x0000000000000000000000000000000000000003`) | RIPEMD-160                 | 600         | data                         | hash          | Hash function                                                                                                                                                            |
| 0x04 (`0x0000000000000000000000000000000000000004`) | identity                   | 15          | data                         | data          | Returns the input                                                                                                                                                        |
| 0x05 (`0x0000000000000000000000000000000000000005`) | modexp                     | 200         | Bsize, Esize, Msize, B, E, M | value         | Arbitrary-precision exponentiation under modulo                                                                                                                          |
| 0x06 (`0x0000000000000000000000000000000000000006`) | ecAdd                      | 150         | x1, y1, x2, y2               | x, y          | Point addition (ADD) on the elliptic curve alt\_bn128                                                                                                                    |
| 0x07 (`0x0000000000000000000000000000000000000007`) | ecMul                      | 6000        | x1, y1, s                    | x, y          | Scalar multiplication (MUL) on the elliptic curve alt\_bn128                                                                                                             |
| 0x08 (`0x0000000000000000000000000000000000000008`) | ecPairing                  | 45000       | x1, y1, x2, y2, ..., xk, yk  | success       | Bilinear function on groups on the elliptic curve alt\_bn128                                                                                                             |
| 0x09 (`0x0000000000000000000000000000000000000009`) | blake2f                    | 0           | rounds, h, m, t, f           | h             | Compression function F used in the BLAKE2 cryptographic hashing algorithm                                                                                                |
| 0x0A (`0x000000000000000000000000000000000000000A`) | point evaluation           | 50000       | bytes                        | bytes         | Verify p(z) = y given commitment that corresponds to the polynomial p(x) and a KZG proof. Also verify that the provided commitment matches the provided versioned\_hash. |
| 0x17 (`0x0000000000000000000000000000000000000017`) | arweave\_upload            | 10003       | bytes                        | bytes         | upload bytes array to Arweave and get back the upload TXID in bytes                                                                                                      |
| 0x18 (`0x0000000000000000000000000000000000000018`) | arweave\_read              | 10003       | bytes                        | bytes         | retrieve an Arweave TXID data in bytes                                                                                                                                   |
| 0x20 (`0x0000000000000000000000000000000000000020`) | read\_block                | 10003       | bytes                        | bytes         | retrieve a LN's block data (from genesis) pulling it from Arweave                                                                                                        |
| 0x21 (`0x0000000000000000000000000000000000000021`) | kyve\_trustless\_api\_blob | 10003       | bytes                        | bytes         | retrieve a historical Ethereum blob data from LN's smart contract layer                                                                                                  |



### Outlining Load Network New Precompiles

#### 1- Precompile 0x17: upload data from Solidity to Arweave

The LN Precompile at address 0x17 (`0x0000000000000000000000000000000000000017`) enables data upload (in byte format) from Solidity to Arweave, and returns the data TXID (in byte format). In Alphanet V4, data uploads are limited to 100KB. Future network updates will remove this limitation and introduce a higher data cap.

**Solidity code example:**

```solidity
pragma solidity ^0.8.0;

contract ArweaveUploader {
    function upload_to_arweave(string memory dataString) public view returns (bytes memory) {
        // Convert the string parameter to bytes
        bytes memory data = abi.encodePacked(dataString);

        // pc address: 0x0000000000000000000000000000000000000017
        (bool success, bytes memory result) = address(0x17).staticcall(data);

        return result;
    }
```

#### 2- Precompile 0x18: read Arweave data from Solidity

This precompile, at address 0x18 (`0x0000000000000000000000000000000000000018`), completes the data pipeline between LN and Arweave, making it bidirectional. It enables retrieving data from Arweave in bytes for a given Arweave TXID.&#x20;

The 0x18 precompile allows user input to choose their Arweave gateway for resolving a TXID. If no gateway URL is provided, the precompile defaults to `arweave.net`.&#x20;

The format of the precompile bytes input (string representation) should be as follows: `gateway_url;arweave_txid`

**Solidity code example:**

```solidity
pragma solidity ^0.8.0;

contract ArweaveReader {
    function read_from_arweave(string memory txIdOrGatewayAndTxId) public view returns (bytes memory) {
        // Convert the string parameter to bytes
        bytes memory data = abi.encodePacked(txIdOrGatewayAndTxId);

        // pc address: 0x0000000000000000000000000000000000000018
        (bool success, bytes memory result) = address(0x18).staticcall(data);

        return result;
    }
}
```

#### 3- Precompile 0x20: Access to LN' historical blocks

This precompile, at address 0x20(`0x0000000000000000000000000000000000000020`), lets smart contract developers not access only the most recent 256 blocks, but any block data starting at genesis. To explain how to request block data using the 0x20 precompile, here is a code example:



```solidity
pragma solidity ^0.8.0;

contract LnBlockReader {
    function read_block() public view returns (bytes memory) {
        // Convert the string parameter to bytes
        string memory blockIdAndField = "141550;hash";
        bytes memory data = abi.encodePacked(blockIdAndField);

        (bool success, bytes memory result) = address(0x20).staticcall(data);

        return result;
    }
}
```

As you can see, for the query variable we have three “parameters” separated by a semicolon ”;” (`gateway;load_block_id;block_field` format)

* An Arweave gateway (optional and fallback to arweave.net if not provided): [https://ar-io.dev](https://ar-io.dev/)
* Load Network's block number to fetch, target block: 141550
* The field of the block struct to access, in this case: hash

Only the gateway is an optional parameter, and regarding the field of the block struct to access, here is the Block struct that the 0x20 precompile uses:

```rust
#[serde(rename_all = "camelCase")]
pub struct Block {
    pub base_fee_per_gas: Option<String>,         // "baseFeePerGas"
    pub blob_gas_used: Option<String>,            // "blobGasUsed"
    pub difficulty: Option<String>,               // "difficulty"
    pub excess_blob_gas: Option<String>,          // "excessBlobGas"
    pub extra_data: Option<String>,               // "extraData"
    pub gas_limit: Option<String>,                // "gasLimit"
    pub gas_used: Option<String>,                 // "gasUsed"
    pub hash: Option<String>,                     // "hash"
    pub logs_bloom: Option<String>,               // "logsBloom"
    pub miner: Option<String>,                    // "miner"
    pub mix_hash: Option<String>,                 // "mixHash"
    pub nonce: Option<String>,                    // "nonce"
    pub number: Option<String>,                   // "number"
    pub parent_beacon_block_root: Option<String>, // "parentBeaconBlockRoot"
    pub parent_hash: Option<String>,              // "parentHash"
    pub receipts_root: Option<String>,            // "receiptsRoot"
    pub seal_fields: Vec<String>,                 // "sealFields" as an array of strings
    pub sha3_uncles: Option<String>,              // "sha3Uncles"
    pub size: Option<String>,                     // "size"
    pub state_root: Option<String>,               // "stateRoot"
    pub timestamp: Option<String>,                // "timestamp"
    pub total_difficulty: Option<String>,         // "totalDifficulty"
    pub transactions: Vec<String>,                // "transactions" as an array of strings
}
```

[Check out the 0x20 source code here](https://github.com/weaveVM/wvm-reth/pull/36/files)

#### 4- Precompile 0x21: Native access to archived Ethereum blobs

This precompile, at address 0x21 (`0x0000000000000000000000000000000000000021`), is a unique solution for native access to blobs data (not just commitments) from the smart contract layer. This precompile fetches from the [KYVE Trustless API](https://docs.kyve.network/access-data-sets/trustless-api/overview) the blobs data that KYVE archives for their supported networks.

Therefore, with 0x21, KYVE clients will have, for the first time, the ability to fetch their archived blobs from an EVM smart contract layer instead of wrapping the Trustless API in oracles and making expensive calls.

0x21 lets you fetch KYVE's Ethereum blob data starting at Ethereum's block [19426589](https://etherscan.io/block/19426589) - the first block with a recorded EIP-4844 transaction. To retrieve a blob from the Trustless API, in the 0x21 staticcall you need to specify the Ethereum block number, blob index in the transaction, and the blob field you want to retrieve, in this format: `block_number;blob_index.field`\
\
&#xNAN;_**N.B: blob\_index represents the blob index in the KYVE’s Trustless API JSON response:**_

```solidity
pragma solidity ^0.8.0;

contract KyveBlobsTrustlessApi {
    function getBlob
() public view returns (bytes memory) {
        // Convert the string parameter to bytes
        string memory query = "20033081;0.blob";
        bytes memory data = abi.encodePacked(query);

        (bool success, bytes memory result) = address(0x21).staticcall(data);

        return result;
    }
}
```

The eip-4844 transaction fields that you can access from the 0x21 query are:

* blob (raw blob data, the body)
* kzg\_commitment
* &#x20;kzg\_proof
* &#x20;slot

**Advantages of 0x21 (use cases)**

* Native access to blob data from smart contract layer
* Access to permanently archived blobs
* Opens up longer verification windows for rollups using KYVE for archived blobs and LN for settlement layer
* Enables using blobs for purposes beyond rollups DA, opening doors for data-intensive blob-based applications with permanent blob access

\
Check out the 0x21 precompile source code [here](https://github.com/weaveVM/wvm-reth/pull/41/files).\
