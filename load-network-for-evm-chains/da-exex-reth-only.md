---
description: Plug Load Network high-throughput DA into any Reth node
---

# DA ExEx (Reth-only)

### About

Adding a DA layer usually requires base-level changes to a network’s architecture. Typically, DA data is posted either by sending calldata to the L1 or through blobs, with the posting done at the sequencer level or by modifying the rollup node’s code.

This ExEx introduces an emerging, non-traditional DA interface for EVM rollups. No changes are required at the sequencer level, and it’s all handled via the ExEx, which is easy to add to any Reth client in just 80 lines of code.

### Integration Tutorial

First, you’ll need to add the following environment variables to your Reth instance:

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption><p>.env</p></figcaption></figure>

The `archiver_pk` refers to the private key of the LN wallet, which is used to pay gas fees on the LN for data posting. The `network` variable points to the path of your network configuration file used for the ExEx. A typical network configuration file looks like this:

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption><p>network.json</p></figcaption></figure>

For a more detailed setup guide for your network, check out this [guide](https://github.com/weaveVM/wvm-archiver?tab=readme-ov-file#add-your-network).

Finally, to implement the Load Network DA ExEx in your Reth client, simply import the DA ExEx code into your ExExes directory and it will work off the shelf with your Reth setup. [Get the code here](https://github.com/weaveVM/wvm-reth/tree/dev/wvm-apps/wvm-exexed/crates/exex-wvm-da).
