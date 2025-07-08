---
description: ELI5 Load Network
---

# ELI5

### What is Load Network?

Load is a high-performance blockchain built towards the goal of solving the EVM storage dilemma with [Arweave](https://arweave.org) and ao [hyperbeam](https://github.com/permaweb/HyperBEAM). It gives the coming generation of high-performance chains a place to settle and store onchain data, without worrying about cost, availability, or permanence.

Load Network offers scalable and cost-effective permanent storage by using Arweave as a decentralized hard drive, both at the node and smart contract layer, hyperbeam as modular stack of evm node components, ad ao network for hyperbeam compute and underlying network security. This makes it possible to store large data sets and run web2-like applications without incurring EVM storage fees.&#x20;

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption><p>Load Network Highlights</p></figcaption></figure>

### Decentralized Full Data Storage Stack

Load Network mainnet is being built to be the highest performing EVM blockchain focusing on data storage, having the largest baselayer transaction input size limit (\~16MB), the largest ever EVM transaction (\~0.5TB 0xbabe transaction), very high network data throughput (multi-gigagas per second), high TPS, decentralization, full data storage stack offering (permanent and temporal), decentralized data gateways and data bundlers.

Load Network achieves high decentralization by using Arweave as decentralized hard drive, hyperbeam as compute marketplace of evm node components, and permissionless block production participation (running a node). Load Network will offer both of permanent data storage and temporal data storage while maintaining decentralized and censorship-resistant data retrieval & ingress (gateways, bundling services, etc).

### Use Cases and How to Integrate&#x20;

#### Ledger Data Storage

Chains like Metis, RSS3 and Dymension use Load Network to permanently store onchain data, acting as a decentralized archival node. If you look at the common problems that are flagged up on [L2Beat](https://l2beat.com/scaling/summary), a lot of it has to do with centralized sources of truth and data that can’t be independently audited or reconstructed in a case where there’s a failure in the chain. LN adds a layer of protection and transparency to L2s, ruling out some of the failure modes of centralization. Learn more about the [wvm-archiver tool here](../load-network-for-evm-chains/ledger-archiver-any-chain.md).

#### High-Throughput Data Availability (DA)

Load Network can plug in to a typical EVM L2's stack as a DA layer that's 10-15x cheaper than solutions like [Celestia and Avail](https://wvm.dev/calculator), and guarantees data permanence on Arweave. LN was built to handle DA for the coming generation of supercharged rollups. With a throughput of \~62MB/s, it could handle DA for [every major L2](https://rollup.wtf) and still have 99%+ capacity left over.

You can check out the custom [DA-ExEx](../load-network-for-evm-chains/da-exex-reth-only.md) to make use of LOAD-DA in any Reth node in less than 80 LoCs, also the [EigenDA-LN Sidecar Server Proxy](../da-integrations/ln-eigenda-proxy-server.md) to use EigenDA's data availability along with Load Network securing its archiving.

#### Storage Heavy dApps

Load Network offers scalable and cost-effective storage by using Arweave as a decentralized hard drive, and hyperbeam as a decentralized cloud. This makes it possible to store large data sets and run web2-like applications without incurring EVM storage fees.&#x20;

We have developed the first-ever Reth precompiles to facilitate, natively, a [bidirectional data pipeline with Arweave](https://blog.wvm.dev/weavevm-arweave-precompiles/) from the smart contract API level. Check out the full list of LN precompiled contracts [here](../using-load-network/supported-precompiles.md).

#### Foundational Layer (L1) For Rollups

Load Network is an EVM compatible blockchain, therefore, rollups can be deployed on LN as same as the rollups state on Ethereum. In contrast to Ethereum or other EVM L1s, rollups deployed on top of LN benefit out-of-the-box from the data-centric features provided by LN (for rollup data settlement and DA).

Rollups deployed on Load Network use the native LN gas token (tLOAD on Alphanet), similar to how ETH is used for OP rollups on Ethereum.

For example, we released a technical guide for developers interested in deploying OP-Stack rollups on LN. [Check it out here](https://github.com/weaveVM/developers/blob/main/guides/op-rollup-deployment.md).&#x20;

#### The Onchain Data Center

Load network is being built with the vision of being the onchain data center. To accomplish this vision, we have started working on several web2 and web3 data pipelines into Load and Arweave, with web2 cloud experience. [Start using LNCP now!](../load-network-cloud-platform/cloud-platform-lncp.md)

### Explore Load Network Ecosystem Dapps (Evolving)

* [Load Network Cloud Platform](../load-network-cloud-platform/cloud-platform-lncp.md) —  The UI of the onchain data center
* [Permacast](https://permacast.app) —  A decentralized media platform on Load Network
* [Tapestry Finance ](https://www.tapestry.fi/)— Uniswap V2 fork
* [shortcuts.bot ](https://shortcuts.bot/)— short links for Load Network txids
* [load.yachts](https://www.load.yachts/) — subdomain resolver for Load Network content
* [onchain.rs ](https://onchain.rs)— Dropbox onchain alternative
* [relic.bot ](https://relic.bot)— Onchain Instagram
* [fairytale.sh ](https://fairytale.sh)— onchain publishing toolkit
* [tokenize.rs ](https://app.gitbook.com/s/z2gd4Irh30FSnal6SJnL/)— Tokenize any data on Load Network
* [bridge.load.network ](https://bridge.load.network)— Hyperlane bridge (Load Alphanet <> Ethereum Holesky)
* [mediadao.xyz](https://mediadao.xyz) — a club for permanent content preservation.
* [Dymension.xyz Roll-Apps ](https://portal.dymension.xyz/rollapps)— deploy a dym roll-app using Load DA

Useful Links

* [Documentation](overview.md)
* [GitHub Organization](https://github.com/weaveVM)
* [Blog](https://blog.wvm.dev)
* [Twitter](https://x.com/weavevm)
* [Discord](https://dsc.gg/wvm)
* [Explorer](https://explorer.wvm.dev)
* [Data storage price calculator](https://wvm.dev/calculator)
* [Alphanet faucet](https://wvm.dev/faucet)\
