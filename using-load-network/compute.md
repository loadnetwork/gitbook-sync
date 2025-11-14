---
description: Explore the compute options within the Load Network sphere
---

# Compute

Using Load network for compute can be wide in options depending on your compute needs, in this page we will go briefly over the different options within the extended Load Network layers.

#### Native EVM compute

The first and most obvious compute layer on the Load Network is the native EVM compute, from smart contracts, using calldata, to eip-4844 blobs, Load Network L1 is an EVM network with large gas capabilities unlocked a big room for compute limitation on the L1.

#### Lua / WASM compute

Given Load Network technical dependencies on ao network in several aspects, the Load Network team runs HyperBEAM nodes (OS nodes running ao compute canonical stack), therefore it's possible to leverage the highly scalable ao compute (processes, hyper-aos, lua serverless functions) within the load stack. Additionally, we have developed evm compute layer (bytecode calculator) as a HyperBEAM device. [Learn more](../load-hyperbeam/evm-1.0-device.md).

#### GPU compute

More in the HyperBEAM land, we developed the Kernel Execution Machine ([\~kem@1.0 device](../load-hyperbeam/kem-1.0-device.md)) - allowing quasi-arbitrary GPU-instructions compute execution  for `.wgsl` functions (shaders, kernels).

#### Reth Execution Extensions (ExEx)

Although this is not compute for the user/consumer level, we have developed several Reth execution extensions (post block-execution compute logic) that can benefits the Reth and the wider EVM developers set. [Learn more](../load-network-exex/about-exexes.md).

