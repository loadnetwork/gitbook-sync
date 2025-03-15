---
description: Reconstruction an EVM network using using its load-archiver node instance
---

# Ledger Archivers: State Reconstruction

### **Understanding the World State Trie**

The World State Trie, also known as the Global State Trie, serves as a cornerstone data structure in Ethereum and other EVM networks. Think of it as a dynamic snapshot that captures the current state of the entire network at any given moment. This sophisticated structure maintains a crucial mapping between account addresses (both externally owned accounts and smart contracts) and their corresponding states.

Each account state in the World State Trie contains several essential pieces of information:

* Current balance of the account
* Transaction nonce (tracking the number of transactions sent from this account)
* Smart contract code (for contract accounts)
* Hash of the associated storage trie (linking to the account’s persistent storage)

This structure effectively represents the current status of all assets and relevant information on the EVM network. Each new block contains a reference to the current global state, enabling network nodes to efficiently verify information and validate transactions.

![EVM Tries](https://gateway.wvm.network/bundle/0x675c3ee485cdc7e8a87b7cf3b109eb0b7558785855f503b42fc7c9ac46093cbb/0)

#### **The Dynamic Nature of State Management**

An important distinction exists between the World State Trie database and the Account Storage Trie database. While the World State Trie database maintains immutability and reflects the network’s global state, the Account Storage Trie database remains mutable with each block. This mutability is necessary because transaction execution within each block can modify the values stored in accounts, reflecting changes in account states as the blockchain progresses.

### **Reconstructing the World State with Load Network Archivers**

The core focus of this article is demonstrating how Load Network Archivers’ data lakes can be leveraged to reconstruct an EVM network’s World State. We’ve developed a proof-of-concept library in Rust that showcases this capability using a customized Revm wrapper. This library abstracts the complexity of state reconstruction into a simple interface that requires just 10 lines of code to implement.

Here’s how to reconstruct a network’s state using our library:

```rust
use evm_state_reconstructing::utils::core::evm_exec::StateReconstructor;  
use evm_state_reconstructing::utils::core::networks::Networks;  
use evm_state_reconstructing::utils::core::reconstruct::reconstruct_network;  
use anyhow::Error;

async fn reconstruct_state() -> Result<StateReconstructor, Error> {  
    let network: Networks = Networks::metis();  
    let state: StateReconstructor = reconstruct_network(network).await?;  
    Ok(state)  
}  
```

The reconstruction process follows a straightforward workflow:

1. The library connects to the specified Load Network Archive network
2. Historical ledger data is retrieved from the Load Network Archiver data lakes
3. Retrieved blocks are processed through our custom minimal EVM execution machine
4. The EVM StateManager applies the blocks sequentially, updating the state accordingly
5. The final result is a complete reconstruction of the network’s World State

This proof-of-concept implementation is available on GitHub: [https://github.com/weaveVM/evm-state-reconstructing](https://github.com/weaveVM/evm-state-reconstructing)

![LN State Reconstuction Flow](https://gateway.wvm.network/bundle/0x675c3ee485cdc7e8a87b7cf3b109eb0b7558785855f503b42fc7c9ac46093cbb/1)



[Load Network Archivers](ledger-archiver-any-chain.md) has evolved beyond its foundation as a decentralized archive node. This proof of concept demonstrates how our comprehensive data storage enables full EVM network state reconstruction - a capability that opens new possibilities for network analysis, debugging, and state verification.

We built this PoC to showcase what’s possible when you combine permanent storage with proper EVM state handling. Whether you’re analyzing historical network states, debugging complex transactions, or building new tools for chain analysis, the groundwork is now laid.
