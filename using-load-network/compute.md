---
description: Explore compute options available with Load Network
---

# Compute

Combining the Load EVM with custom HyperBEAM devices enables a wide range of options, from **smart contracts** to **WASM workers** to **serverless GPU functions**.

In this page we will briefly go over the different options within the extended Load Network ecosystem.

#### Native EVM compute

The Load EVM offers native EVM compute with typical smart contracts, blobs and calldata standards that are all 1:1 compatible with Ethereum and other EVM networks. As a specialized storage and DA chain, the Load L1 is optimized for [high throughput](../about-load-network/key-features.md) to enable data-intensive dApps. [Connect to Load Alphanet RPC here](network-configurations.md).

#### Lua / WASM compute

Load Network is a hybrid of the EVM and AO's HyperBEAM. The Load Network team runs HyperBEAM instances (permissionless nodes running the AO compute canonical stack), therefore it's possible to leverage the highly scalable AO compute (processes, hyper-aos, Lua serverless functions) within the Load stack. [Connect to a node here](../).

Additionally, we have developed an EVM compute layer (bytecode calculator) as a HyperBEAM device. [Learn more](../load-hyperbeam/evm-1.0-device.md).

#### GPU compute

More over in HyperBEAM land, the Kernel Execution Machine ([\~kem@1.0 device](../load-hyperbeam/kem-1.0-device.md)) allows for quasi-arbitrary GPU-instructions compute execution  for `.wgsl` functions (shaders, kernels). [Get started](../load-hyperbeam/kem-1.0-device.md).

#### Reth Execution Extensions (ExEx)

Although this is not compute for the user/consumer level, Load maintains several Reth execution extensions (post block-execution compute logic) that can benefits the Reth and the wider EVM developers set. [Learn more](../load-network-exex/about-exexes.md).

