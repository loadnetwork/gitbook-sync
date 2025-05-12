---
description: Load Network custom HyperBEAM devices
---

# About Load HyperBEAM

### About HyperBEAM

HyperBeam is a client implementation of the AO-Core protocol, written in Erlang. It can be seen as the 'node' software for the decentralized operating system that AO enables; abstracting hardware provisioning and details from the execution of individual programs.

HyperBEAM node operators can offer the services of their machine to others inside the network by electing to execute any number of different `devices`, charging users for their computation as necessary.

Each HyperBEAM node is configured using the `~meta@1.0` device, which provides an interface for specifying the node's hardware, supported devices, metering and payments information, amongst other configuration options. For more details, check out the HyperBEAM codebase: [https://github.com/permaweb/HyperBEAM](https://github.com/permaweb/HyperBEAM)&#x20;

### load\_hb: Load Network HyperBEAM node with custom devices

#### EVM Device

Starting with the current and defacto ethereum’s virtual machine (EVM), we have created a hyperbeam device that port Revm (Rust implementation of the Ethereum Virtual Machine) as a NIF (Native Implemented Function) in the hyperbeam codebase.

The evm device is designed to take an EVM appchain configuration (gas limit, chain id, chain name) , interpret a given signed raw transaction (Create or Call transaction kind), and update the appchain’s state given the results from the state transitions.

Therefore, this evm device not just offers EVM execution, but also statefulness of a given context. As an implementation example, we have deployed an appchain with a deployed Uniswap V2, another with [zama fhevm smart contracts](https://github.com/zama-ai/fhevm-solidity) layer, and a basic appchain with basic Ethereum functionalities.

For example, with the Uniswap V2 appchain we deployed a wrapped native gas token, multicall33 contract, uniswap factory contract, router contract, and tested the contracts stack.\
With zama’s fhevm, we deployed all of the zama L2 smart contracts: proxy contract, ACL, fhevm executor, KMS verifier, fhevm gaslimit, inputverifier, decryption oracle, we initialied all of the contracts then deployed and minted `EncryptedERC20` token. Check the full set of tests [here](https://github.com/loadnetwork/load_hb/blob/main/native/load_revm_nif/src/tests)

EVM device source code: [https://github.com/loadnetwork/load\_hb/tree/main/native/load\_revm\_nif](https://github.com/loadnetwork/load_hb/tree/main/native/load_revm_nif)

#### RISC-V Device

we have developed a custom fork of [R55](https://github.com/loadnetwork/r55) (an Ethereum Execution Environment that seamlessly integrates RISCV smart contracts alongside traditional EVM smart contracts) to [handle signed raw transaction](https://github.com/loadnetwork/r55/blob/main/r55/src/exec.rs#L27) input and return the resulted computed EVM db.

After getting R55 to work with the OOTB interpretation of signed raw transaction, we built on top of it a hyperbeam device offering RISC-V compatible Ethereum appchains.\
For example, this erc20.rs Rust smart contract was deployed on a hb risc-v appchain: [github.com/loadnetwork/r55](https://github.com/loadnetwork/r55/blob/main/examples/erc20/src/lib.rs)

RISC-V custom device source code: [https://github.com/loadnetwork/load\_hb/tree/main/native/riscv\_em\_nif](https://github.com/loadnetwork/load_hb/tree/main/native/riscv_em_nif) \
