---
description: Load Network Compatibility with the standards
---

# Compatibility & Performance

### EVM compatibility

Load Network EVM is built on top of Reth, making it compatible as a network with existing EVM-based applications. This means you can run your current Ethereum-based projects on LN without significant modifications, leveraging the full potential of the EVM ecosystem.

Load Network EVM doesn't introduce new opcodes or breaking changes to the EVM itself, but it uses ExExes and adds custom precompiles:

#### Alphanet V0.5.3

* **gas per byte:** 8
* **gas limit:** 500\_000\_000
* **block time:** 1s
* **gas/s:** 500 mg/s
* **data throughput:** \~62 MBps
