---
description: The RISC-V Execution Machine device
---

# \~riscv-em@1.0 device

{% hint style="danger" %}
This device is in a very Proof Of Concept stage
{% endhint %}

## About

we have developed a custom fork of [R55](https://github.com/loadnetwork/r55) (an Ethereum Execution Environment that seamlessly integrates RISCV smart contracts alongside traditional EVM smart contracts) to [handle signed raw transaction](https://github.com/loadnetwork/r55/blob/main/r55/src/exec.rs#L27) input and return the resulted computed EVM db.

After getting R55 to work with the OOTB interpretation of signed raw transaction, we built on top of it a hyperbeam device offering RISC-V compatible Ethereum appchains.\
For example, this erc20.rs Rust smart contract was deployed on a hb risc-v appchain: [github.com/loadnetwork/r55](https://github.com/loadnetwork/r55/blob/main/examples/erc20/src/lib.rs)

RISC-V custom device source code: [https://github.com/loadnetwork/load\_hb/tree/main/native/riscv\_em\_nif](https://github.com/loadnetwork/load_hb/tree/main/native/riscv_em_nif)&#x20;
