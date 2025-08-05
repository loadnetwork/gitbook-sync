---
description: >-
  bundles-rs is a Rust SDK for creating, signing, managing and posting ANS-104
  dataitems
---

# Arweave's ANS-104 Rust SDK

### About

A Rust SDK for creating, signing, managing and posting [ANS-104 dataitems](https://github.com/ArweaveTeam/arweave-standards/blob/master/ans/ANS-104.md).

> Warning: this repository is actively under development and could have breaking changes until reaching full API compatibility in v1.0.0.

### Installation

Add to your `Cargo.toml`:

```toml
[dependencies]
# main library
bundles_rs = { git = "https://github.com/loadnetwork/bundles-rs", branch = "main" }

# use individual crates
# or use branch/tag/rev -- we recommend checking and using the last client version
ans104 = { git = "https://github.com/loadnetwork/bundles-rs", version = "0.1.0" } 
crypto = { git = "https://github.com/loadnetwork/bundles-rs", version = "0.1.0" }
```

#### Dev setup

```sh
git clone https://github.com/loadnetwork/bundles-rs.git
cd bundles-rs
cargo clippy --workspace --lib --examples --tests --benches --locked --all-features
cargo +nightly fmt
cargo check --all
```

#### Supported Signers

| Blockchain |             Signature Type             |
| :--------: | :------------------------------------: |
|   Arweave  |                 RSA-PSS                |
|  Ethereum  |                secp256k1               |
|   Solana   | Ed25519 (with base58 solana flavoring) |
|      -     |        Ed25519Core (raw Ed25519)       |

#### Regarding Tags

This ANS-104 dataitems client fully implements the ANS-104 specification as-is

|         Constraint         |     bundles-rs    | [Spec](https://github.com/ArweaveTeam/arweave-standards/blob/master/ans/ANS-104.md) | [arbundles js](https://github.com/DHA-Team/arbundles) | [HyperBEAM ar\_bundles](https://github.com/permaweb/HyperBEAM/blob/edge/src/ar_bundles.erl) |
| :------------------------: | :---------------: | :---------------------------------------------------------------------------------: | :---------------------------------------------------: | :-----------------------------------------------------------------------------------------: |
| Maximum tags per data item |    <= 128 tags    |                                     <= 128 tags                                     |                      <= 128 tags                      |                                         No max tags                                         |
|      Tag name max size     |     1024 bytes    |                                      1024 bytes                                     |             all keys + vals <= 4096 bytes             |                                    Can have empty strings                                   |
|     Tag value max size     |     3072 bytes    |                                      3072 bytes                                     |                 Can have empty strings                |                                      val <= 3072 bytes                                      |
|     Empty names/values     | non empty strings |                                  non empty strings                                  |                 Can have empty strings                |                                    Can have empty strings                                   |

### Usage Examples

#### Quick start

```rust
use bundles_rs::{
    ans104::{data_item::DataItem, tags::Tag},
    crypto::ethereum::EthereumSigner,
};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // create a signer
    let signer = EthereumSigner::random()?;
    
    // create tags (metadata)
    let tags = vec![
        Tag::new("Content-Type", "text/plain"),
        Tag::new("App-Name", "Load-Network"),
    ];
    
    // create and sign a dataitem
    let data = b"Hello World Arweave!".to_vec();
    // first None for Target and the second for Anchor
    // let target = [0u8; 32]; -- 32-byte target address
    // let anchor = b"unique-anchor".to_vec(); -- max 32 bytes
    let item = DataItem::build_and_sign(&signer, None, None, tags, data)?;
    
    // get the dataitem id
    let id = item.arweave_id();
    println!("dataitem id: {}", id);
    
    // serialize for upload
    let bytes = item.to_bytes()?;
    println!("Ready to upload {} bytes", bytes.len());
    
    Ok(())
}
```

**Or for basic signed dataitem**

```rust
use bundles_rs::ans104::{data_item::DataItem, tags::Tag};

// create unsigned data item
let tags = vec![Tag::new("Content-Type", "application/json")];
let data = br#"{"message": "Hello World"}"#.to_vec();
let mut item = DataItem::new(None, None, tags, data)?;

// sign dataitem
item.sign(&signer)?;
```

#### Working with signers

_**N.B: use random signer generation for testing purposes only**_

**Arweave Signer**

```rust
use bundles_rs::crypto::arweave::ArweaveSigner;

let signer = ArweaveSigner::from_jwk_file("wallet.json")?;

// from stringified JWK
let jwk_json = r#"{"kty":"RSA","n":"...","e":"AQAB","d":"..."}"#;
let signer = ArweaveSigner::from_jwk_str(jwk_json)?;

// random
let signer = ArweaveSigner::random()?;

// Arweave address
let address = signer.address();
println!("Arweave address: {}", address);
```

**Ethereum Signer**

```rust
use bundles_rs::crypto::ethereum::EthereumSigner;

// generate random key
let signer = EthereumSigner::random()?;

// or from private key bytes
let private_key = hex::decode("your_private_key_hex")?;
let signer = EthereumSigner::from_bytes(&private_key)?;

// EOA
let address = signer.address_string();
println!("Ethereum address: {}", address);
```

**Solana Signer**

```rust
use bundles_rs::crypto::solana::SolanaSigner;
// random
let signer = SolanaSigner::random();
// pk
let signer = SolanaSigner::from_base58("your_base58_private_key")?;
// from secret bytes
let secret = [0u8; 32]; // your secret bytes
let signer = SolanaSigner::from_secret_bytes(&secret)?;

// Get Solana address
let address = signer.address();
println!("Solana address: {}", address);
```

**Ed25519Core Signer**

```rust
use bundles_rs::crypto::ed25519::Ed25519Core;

// random
let signer = Ed25519Core::random();
// from seed bytes
let seed = [0u8; 32];
let signer = Ed25519Core::from_secret_bytes(&seed)?;
```

#### Verification

**Manual**

```rust
// verify signature and structure
item.verify()?;

// manual verification steps
assert_eq!(item.signature.len(), item.signature_type.signature_len());
assert_eq!(item.owner.len(), item.signature_type.owner_len());
```

**With Signer**

```rust
use bundles_rs::crypto::signer::Signer;

let message = item.signing_message();
let is_valid = signer.verify(&message, &item.signature)?;
assert!(is_valid);
```

#### Deep hash

```rust
use bundles_rs::ans104::deep_hash::{DeepHash, deep_hash_sync};

let data = b"custom data";
let hash_structure = DeepHash::List(vec![
    DeepHash::Blob(b"custom"),
    DeepHash::Blob(data),
]);

let hash = deep_hash_sync(&hash_structure);
println!("Deep hash hex: {}", hex::encode(hash));
```

#### Upload to Bundling services over HTTP (e.g. [Turbo](https://ardrive.io/turbo-bundler))

```rust
use reqwest::Client;

async fn upload_to_turbo(item: &DataItem) -> Result<String, Box<dyn std::error::Error>> {
    let client = Client::new();
    let bytes = item.to_bytes()?;
    
    let response = client
        .post("https://turbo.ardrive.io/tx/solana")
        .header("Content-Type", "application/octet-stream")
        .body(bytes)
        .send()
        .await?;
    
    if response.status().is_success() {
        let tx_id = response.text().await?;
        Ok(tx_id)
    } else {
        Err(format!("Upload failed: {}", response.status()).into())
    }
}
```

### bundler crate

`bundler` crate is Rust SDK to interact with Arweave (ANS-104) bundling services. This crate is designed to be backward compatible with existing bundling services and fine tuned for [Turbo](https://turbo.ardrive.io/)

### Installation

```toml
[dependencies]
# main library
bundles_rs = { git = "https://github.com/loadnetwork/bundles-rs", branch = "main" }

# bundler only
bundler = { git = "https://github.com/loadnetwork/bundles-rs", branch = "main" }
```

#### Imports

```rust
use bundles_rs::bundler::BundlerClient;
use bundles_rs::ans104::{data_item::DataItem, tags::Tag};
use bundles_rs::crypto::solana::SolanaSigner;
```

### Usage Example

#### Send Transaction (Solana)

```rust
let client = BundlerClient::new().url("https://upload.ardrive.io").build().unwrap();
let signer = SolanaSigner::random();
let tags = vec![Tag::new("content-type", "text/plain")];
let dataitem = DataItem::build_and_sign(&signer, None, None, tags, b"hello world".to_vec()).unwrap();

let tx = client.send_transaction(dataitem).await.unwrap();
println!("tx: {:?}", tx);
```

#### Send Transaction (Turbo)

```rust
let client = BundlerClient::turbo().build().unwrap();
let signer = SolanaSigner::random();
let tags = vec![Tag::new("content-type", "text/plain")];
let dataitem = DataItem::build_and_sign(&signer, None, None, tags, b"hello world turbo".to_vec()).unwrap();

let tx = client.send_transaction(dataitem).await.unwrap();
println!("tx: {:?}", tx);
```

#### Get Default Client Info

```rust
let client = BundlerClient::default().build().unwrap();
let info = client.info().await.unwrap();
println!("{:?}", info);
```

#### Get Turbo Client Info

```rust
let client = BundlerClient::turbo().build().unwrap();
let info = client.info().await.unwrap();
println!("{:?}", info);
```

#### Get Price for Bytes (Turbo)

```rust
let client = BundlerClient::turbo().build().unwrap();
let price = client.bytes_price(99999).await.unwrap();
println!("{:?}", price);
```

#### Get Rates (Turbo)

```rust
let client = BundlerClient::turbo().build().unwrap();
let rates = client.get_rates().await.unwrap();
println!("{:?}", rates);
```

#### Check Transaction Status (Turbo)

```rust
let client = BundlerClient::turbo().build().unwrap();
let status = client.status("w5n6r6PvqBRph2or4WiyjLumL9HE-IR_JgEcnct_3b0").await.unwrap();
println!("{:?}", status);
```

### Turbo API References:

* upload api: https://upload.ardrive.io/api-docs
* payment api: https://payment.ardrive.io/api-docs

SDK source code: [https://github.com/loadnetwork/bundles-rs](https://github.com/loadnetwork/bundles-rs)&#x20;
