---
description: Load Network custom HyperBEAM devices
---

# About Load HyperBEAM

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p>hb.load.rs</p></figcaption></figure>

### About HyperBEAM

HyperBeam is a client implementation of the AO-Core protocol, written in Erlang. It can be seen as the 'node' software for the decentralized operating system that AO enables; abstracting hardware provisioning and details from the execution of individual programs.

HyperBEAM node operators can offer the services of their machine to others inside the network by electing to execute any number of different `devices`, charging users for their computation as necessary.

Each HyperBEAM node is configured using the `~meta@1.0` device, which provides an interface for specifying the node's hardware, supported devices, metering and payments information, amongst other configuration options. For more details, check out the HyperBEAM codebase: [https://github.com/permaweb/HyperBEAM](https://github.com/permaweb/HyperBEAM)&#x20;

### load\_hb: Load Network HyperBEAM node with custom devices

The [load\_hb](https://github.com/loadnetwork/load_hb) repository is our HyperBEAM fork with custom devices such as [\~evm@1.0](evm-1.0-device.md), [\~kem@1.0](kem-1.0-device.md), and [\~riscv-em@1.0](riscv-em-1.0-device.md)

Our development motto is driven by the [Hyperbeam Accelerationism (hb/acc) ](https://blog.decent.land/hb-acc/)manifesto initiated during Arweave Day Berlin 2025.

Our main hyperbeam development is hosted on [hb.load.rs](https://hb.load.rs/)
