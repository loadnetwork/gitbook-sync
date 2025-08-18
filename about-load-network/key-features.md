---
description: Exploring Load Network key features
---

# Key Features&#x20;

Let's explore the key features of Load Network:

### Beefy Block Producer

Load Network achieves enterprise-like performance by limiting block production to beefy hardware nodes while maintaining trustless and decentralized block validation.

What this means, is that anyone with a sufficient amount of $AO tokens (read more about $AO security below in this page) meeting the PoS staking threshold, plus the necessary hardware and internet connectivity (super-node, enterprise hardware), can run a node. This approach is inspired by Vitalik Buterin's work in ["The Endgame"](https://vitalik.eth.limo/general/2021/12/06/endgame.html) post.

> **Block&#x20;**_**production**_**&#x20;is centralized, block&#x20;**_**validation**_**&#x20;is trustless and highly decentralized, and censorship is still prevented**.

These "super nodes" producing Load Network blocks result in a high-performance EVM network.

### Large Block Size

Raising the gas limit increases the block size and operations per block, affecting both History growth and State growth (mainly relevant for our point here).

Load Network Alphanet raises the gas limit to 500M gas (doing 500 mg/s), and lowers the gas per non-zero byte to 8. These changes have resulted in a larger max theoretical block size of 62 MB, and consequently, the network data throughput is \~62 MBps.

This high data throughput can be handled thanks to block production by super nodes and hardware acceleration.

### &#x20;High-Throughput DA

Up until now, there's been no real-world, scalable DA (altDA) layer ready to handle high data throughput with permanent storage. Load Alphanet reaches a maximum throughput of 62 MBps, with a projection of 125 MBps in mainnet.

### Modular EVM Node Components Design

Building on HyperBEAM and ao network enables us to package modules of the EVM node stack as HyperBEAM NIF (Native Implemented Function) devices.

This horizontally scalable and parallel architecture allows Load Network EVM nodes to be modularly composable in a totally new paradigm. For example, a node run by Alice might not implement the JSON-RPC component but can pay fees (compute paid in $AO) for its usage from Bob, who has this missing EVM component.

With this model, we will be achieving ao network synergy and interoperability. To read more about the rationale behind this, check the "[mission π](https://blog.decent.land/mission-pi/)" blog post

### Programmable EVM data & Arweave Permanence

Load Network uses a set of Reth execution extensions (ExExes) to serialize each block in Borsh, then compress it in Brotli before sending it to Arweave. These computations ensure a cost-efficient, permanent history backup on Arweave. This feature is crucial for other L1s/L2s using Load Network for data settlement, aka LOADing \[ ^^].&#x20;

In the [diagrams & benchmarks here](https://github.com/weaveVM/wvm-research), we show the difference between various compression algorithms applied to Borsh-serialized empty block (zero transactions) and JSON-serialized empty block. Borsh serialization combined with Brotli compression gives us the most efficient compression ratio in the data serialization-compression process.

At the time of writing, and since the data protocol's inception, Load Network Arweave ExEx is the largest data protocol on top of Arweave in terms of the number of settled dataitems.

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

The Load Network interface with Arweave with more than just block data settling. We have developed the first precompiles that achieve a native **bidirectional data pipeline** with the Arweave network. In other words, with these precompiles (currently supported by Load Network testnet), you can read data from Arweave and send data to Arweave trustlessly and natively from a Solidity smart contract, creating the first ever programmable scalable EVM data, backed with Arweave permanence. [Learn more about Load Network precompiles in this section.](../using-load-network/supported-precompiles.md)\


### Cost Efficient Data Settling

Load's hyper computation, supercharged hardware, and interface with Arweave results in significantly cheaper data settlement costs on Load Network, which include the Arweave fees to cover the archiving costs. [Check the comparison calculator for realtime data](https://www.wvm.dev/calculator).

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption><p>Data LOADing cost comparison</p></figcaption></figure>

Even compared to temporary blob-based solutions, Load Network still offers a significantly cheaper permanent data solution (calldata).

### Alien Stack, Alien Network Security

The Load Network is the first EVM L1 to leverage Arweave storage and interoperability natively inside the EVM, and obviously, the first EVM L1 to adopt the modular evm node components paradigm powered by HyperBEAM devices.

To align this alien tech stack, we needed an alien security model.  On Load Network, users will pay gas in Load's native gas token, $LOAD.  On the node operator side, a node that is not running the full stack of EVM components, is required to buy compute from other nodes offering the missing component by paying $AO.

Additionally, as Load is built on top of HyperBEAM (ao network) and Arweave, it's logical to inherit the network security of AO and reinforce Load's self-DA security. For these reasons, Load node operators stake $AO in order to join the EVM L1 block production.
