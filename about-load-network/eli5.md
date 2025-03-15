---
description: ELI5 Load Network
---

# ELI5

### What is Load Network?

Load is a high-performance blockchain built towards the goal of solving the EVM storage dilemma with [Arweave](https://arweave.org) and [EigenLayer](https://www.eigenlayer.xyz/). It gives the coming generation of high-performance chains a place to settle and store onchain data, without worrying about cost, availability, or permanence.

Load Network offers scalable and cost-effective permanent storage by using Arweave as a decentralized hard drive, both at the node and smart contract layer, and EigenLayer as stack decentralization offering temporal storage and decentralizing the cloud infrastructure and data access of LN. This makes it possible to store large data sets and run web2-like applications without incurring EVM storage fees. Load Network's storage as calldata [costs around $0.05/MB, compared with Ethereum’s $450/MB.](https://wvm.dev/calculator)

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption><p>Load Network Highlights</p></figcaption></figure>

### Decentralized Full Data Storage Stack

Load Network mainnet is being built to be the highest performing EVM blockchain focusing on data storage, having the largest baselayer transaction input size limit (\~16MB), the largest ever EVM transaction (\~0.5TB 0xbabe transaction), very high network data throughput (multi-gigagas per second), high TPS, decentralization, full data storage stack offering (permanent and temporal), decentralized data gateways and data bundlers.

Load Network achieves high decentralization by using Arweave as decentralized hard drive, EigenLayer as decentralized cloud stack, and allowing network participation (nodes) . Load Network will offer both of permanent data storage and temporal data storage while maintaining decentralized and censorship-resistant data retrieval & ingress (gateways, bundling services, etc).

### Use Cases and How to Integrate&#x20;

#### Ledger Data Storage

Chains like Metis, RSS3 and Dymension use Load Network to permanently store onchain data, acting as a decentralized archival node. If you look at the common problems that are flagged up on [L2Beat](https://l2beat.com/scaling/summary), a lot of it has to do with centralized sources of truth and data that can’t be independently audited or reconstructed in a case where there’s a failure in the chain. LN adds a layer of protection and transparency to L2s, ruling out some of the failure modes of centralization. Learn more about the [wvm-archiver tool here](../load-network-for-evm-chains/ledger-archiver-any-chain.md).

#### High-Throughput Data Availability (DA)

Load Network can plug in to a typical EVM L2's stack as a DA layer that's 10-15x cheaper than solutions like [Celestia and Avail](https://wvm.dev/calculator), and guarantees data permanence on Arweave. LN was built to handle DA for the coming generation of supercharged rollups. With a throughput of \~62MB/s, it could handle DA for [every major L2](https://rollup.wtf) and still have 99%+ capacity left over.

You can check out the custom [DA-ExEx](../load-network-for-evm-chains/da-exex-reth-only.md) to make use of LOAD-DA in any Reth node in less than 80 LoCs, also the [EigenDA-LN Sidecar Server Proxy](../da-integrations/ln-eigenda-proxy-server.md) to use EigenDA's data availability along with Load Network securing its archiving.

#### Storage Heavy dApps

Load Network offers scalable and cost-effective storage by using Arweave as a decentralized hard drive, and EigenLayer as a decentralized cloud. This makes it possible to store large data sets and run web2-like applications without incurring EVM storage fees.&#x20;

We have developed the first-ever Reth precompiles to facilitate, natively, a [bidirectional data pipeline with Arweave](https://blog.wvm.dev/weavevm-arweave-precompiles/) from the smart contract API level. Check out the full list of LN precompiled contracts [here](../using-load-network/load-network-precompiles.md).

#### Foundational Layer (L1) For Rollups

Load Network is an EVM compatible blockchain, therefore, rollups can be deployed on LN as same as the rollups state on Ethereum. In contrast to Ethereum or other EVM L1s, rollups deployed on top of LN benefit out-of-the-box from the data-centric features provided by LN (for rollup data settlement and DA).

Rollups deployed on Load Network use the native LN gas token (tLOAD on Alphanet), similar to how ETH is used for OP rollups on Ethereum.

For example, we released a technical guide for developers interested in deploying OP-Stack rollups on LN. [Check it out here](https://github.com/weaveVM/developers/blob/main/guides/op-rollup-deployment.md).&#x20;

### Full Cloud Stack

Load Network is being built to serve as an onchain data center, offering a full stack of solutions for decentralized storage and data management:

#### Key Components:

* **Permanent Data Storage**: Through LN native integration with Arweave via ExExes and precompiles.
* **Temporal Data Storage**: Via Load Network LOAD2 AVS
* **Decentralized Infrastructure**: Including gateways and a bundlers network functioning as an EigenLayer AVS (LOAD1)

#### LOAD1 Functionality:

LOAD1 AVS, powered by EigenLayer, will decentralize the access of Load Network Bundlers built on top of LN Bundler's data protocol network (which is already live on Alphanet v0.4.0 — permissionless). Decentralizing 0xbabe2 data broadcasting and retrieval via LOAD1, will unlock the largest EVM transaction (0xbabe2) wider in the Ethereum & EigenLayer ecosystem, giving access to 492GB size bundle.

### Explore Load Network Ecosystem Dapps (Evolving)

* [onchain.rs ](https://onchain.rs)— Dropbox onchain alternative
* [relic.bot ](https://relic.bot)— Onchain Instagram
* [fairytale.sh ](https://fairytale.sh)— onchain publishing toolkit
* [swarm-importer](https://blog.wvm.dev/swarm-importer/) — import Swarm data to Load Network
* [wvm-lassie](https://github.com/weaveVM/wvm-lassie) — Filecoin/IPFS data importer to Load Network&#x20;
* [ipfs.rs ](https://ipfs.rs)— IPFS pinning service with Load Network permanent storage sidecar

Useful Links

* [Documentation](overview.md)
* [GitHub Organization](https://github.com/weaveVM)
* [Blog](https://blog.wvm.dev)
* [Twitter](https://x.com/weavevm)
* [Discord](https://dsc.gg/wvm)
* [Explorer](https://explorer.wvm.dev)
* [Data storage price calculator](https://wvm.dev/calculator)
* [Alphanet faucet](https://wvm.dev/faucet)\
