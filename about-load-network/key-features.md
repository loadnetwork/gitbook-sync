---
description: Exploring Load Network key features
---

# Key Features&#x20;

Let's explore the key features of Load Network:

### Beefy Block Producer

Load Network achieves enterprise-like performance by limiting block production to beefy hardware nodes while maintaining trustless and decentralized block validation.

What this means, is that anyone with a sufficient amount of $LOAD tokens meeting the PoS staking threshold, plus the necessary hardware and internet connectivity (super-node, enterprise hardware), can run a node. This approach is inspired by Vitalik Buterin's work in ["The Endgame"](https://vitalik.eth.limo/general/2021/12/06/endgame.html) post.

> **Block&#x20;**_**production**_**&#x20;is centralized, block&#x20;**_**validation**_**&#x20;is trustless and highly decentralized, and censorship is still prevented**.

These "super nodes" producing Load Network blocks result in a high-performance EVM network.

### Large Block Size

Raising the gas limit increases the block size and operations per block, affecting both History growth and State growth (mainly relevant for our point here).

Load Network Alphanet V2 (formerly WeaveVM V2) has raised the gas limit to 500M gas (doing 500 mg/s), and lowered the gas per non-zero byte to 8. These changes have resulted in a larger max theoretical block size of 62 MB, and consequently, the network data throughput is \~62 MBps.

This high data throughput can be handled thanks to the approach of beefy block production by super nodes and hardware acceleration.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Ethereum Scaling Bottlenecks</p></figcaption></figure>

### &#x20;High-Throughput DA

Up until now, there's been no real-world, scalable DA layer ready to handle high data throughput with permanent storage. In LOAD Alphanet V2, we've already reached 62 MBps with a projection to hit 125 MBps in mainnet.

### Parallel Execution

To reduce the gas fees consumed by EVM opcode execution, we're aiming to use a parallel execution EVM client for the Reth node in mainnet.

### EVM interface for Arweave Data: Permanent History

Load Network uses a set of Reth execution extensions (ExExes) to serialize each block in Borsh, then compress it in Brotli before sending it to Arweave. These computations ensure a cost-efficient, permanent history backup on Arweave. This feature is crucial for other L1s/L2s using LOAD for data settling (LOADing).&#x20;

In the [diagrams & benchmarks here](https://github.com/weaveVM/wvm-research), we show the difference between various compression algorithms applied to Borsh-serialized empty block (zero transactions) and JSON-serialized empty block.&#x20;

And we can see that Borsh serialization combined with Brotli compression gives us the most efficient compression ratio in the data serialization-compression process.

### Cost Efficient Data Settling

LOAD's hyper computation, supercharged hardware, and interface with Arweave result in significantly cheaper data settlement costs on Load Network, which include the Arweave fees to cover the archiving costs. [Check the comparison calculator for realtime data](https://www.wvm.dev/calculator).

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption><p>Data LOADing cost comparison</p></figcaption></figure>

Even compared to temporary blob-based solutions, Load Network still offers a significantly cheaper permanent data solution (calldata).

### Load is L0 for EVM L1s/L2s

Load Network can be used as either a DA solution or for data settlement (like Ethereum). Since storing data on Load Network is very cheap compared to other EVM solutions, the network can be labeled as an L0 for other L1s or L2s.

Load Network offers self-DA secured by network economics along with a permanent data archive, secured by [Arweave](https://arweave.org).

### Bidirectional data pipeline with Arweave

The LOAD team has developed the first precompiles that achieve a native bidirectional data pipeline with the Arweave network. In other words, with these precompiles (currently supported by Load Network testnet), you can read data from Arweave and send data to Arweave trustlessly and natively from a Solidity smart contract. [Learn more about Load Network precompiles in this section.](../using-load-network/load-network-precompiles.md)\
