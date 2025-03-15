---
description: Guidance on How To Deploy OP-Stack Rollups on Laod Network
---

# Deploying OP-Stack Rollups

### About the OP Stack

The [OP Stack](https://docs.optimism.io/stack/getting-started) is a generalizable framework spawned out of Optimism’s efforts to scale the Ethereum L1. It provides the tools for launching a production-quality Optimistic Rollup blockchain with a focus on modularity. Layers like the sequencer, data availability, and execution environment can be swapped out to create novel L2 setups.

The goal of optimistic rollups is to increase L1 transaction throughput while reducing transaction costs. For example, when Optimism users sign a transaction and pay the gas fee in ETH, the transaction is first stored in a private mempool before being executed by the sequencer. The sequencer generates blocks of executed transactions every two seconds and periodically batches them as call data submitted to Ethereum. The “optimistic” part comes from assuming transactions are valid unless proven otherwise.

In the case of Laod Network, we have modified OP Stack components to use LN as the data availability and settlement layer for L2s deployed using this architecture.

### OP Stack Rollups on Load Network

We’ve built on top of the [Optimism Monorepo](https://github.com/ethereum-optimism/optimism) to enable the deployment of optimistic rollups using LN as the L1. The key difference between deploying OP rollups on Load Network versus Ethereum is that when you send data batches to LN, your rollup data is also permanently archived on Arweave via to [LN’s Execution Extensions (ExExes).](../load-network-exex/exex.rs.md)

As a result, OP Stack rollups using LN for data settlement and data availability (DA) will benefit from the cost-effective, permanent data storage offered by Load Network and Arweave. Rollups deployed on LN use the native network gas token (tLOAD on Alphanet), similar to how ETH is used for OP rollups on Ethereum.

**We’ve released a detailed technical guide on GitHub for developers looking to deploy OP rollups on Load Network. Check it out** [**here**](https://github.com/weaveVM/developers/blob/main/guides/op-rollup-deployment.md) **and the** [**LN’s fork of Optimism Monorepo here**.](https://github.com/weaveVM/optimism/tree/deploy-op-stack-rollup-on-wvm-l1)
