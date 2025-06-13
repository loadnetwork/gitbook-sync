---
description: The first Revm EVM device on HyperBEAM
---

# \~evm@1.0 device

## About

The `@evm1.0` device: an EVM bytecode emulator built on top of Revm (version [v22.0.1](https://github.com/bluealloy/revm/releases/tag/v69)).

The device not only allows evaluation of bytecode (signed raw transactions) against a given state db, but also supports appchain creation, statefulness, EVM context customization (gas limit, chain id, contract size limit, etc.), and the elimination of the block gas limit by substituting it with a transaction-level gas limit.

{% hint style="warning" %}
_**This device is experimental, in PoC stage**_&#x20;

Live demo at [ultraviolet.load.network](https://github.com/loadnetwork/load_hb/blob/main/native/load_revm_nif/ultraviolet.load.network)
{% endhint %}

## Technical Architecture

`eval_bytecode()` takes 3 inputs, a signed raw transaction (N.B: chain id matters), a JSON-stringified state db and the output state path (here in this device it's in [./appchains](https://github.com/loadnetwork/load_hb/blob/main/native/load_revm_nif/appchains))

```rust
/#[rustler::nif]
fn eval_bytecode(signed_raw_tx: String, state: String, cout_state_path: String) -> NifResult<String> {
    let state_option = if state.is_empty() { None } else { Some(state) };
    let evaluated_state: (String, String) = eval(signed_raw_tx, state_option, cout_state_path)?;
    Ok(evaluated_state.0)
}

#[rustler::nif]
fn get_appchain_state(chain_id: &str) -> NifResult<String> {
	let state = get_state(chain_id);
    Ok(state)
}
```

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

### References

* device source code: [native/load\_revm\_nif](https://github.com/loadnetwork/load_hb/tree/main/native/load_revm_nif)
* hb device interface: [dev\_evm.erl](https://github.com/loadnetwork/load_hb/blob/main/src/dev_evm.erl)
* nif tests: [load\_revm\_nif\_test.erl](https://github.com/loadnetwork/load_hb/blob/main/src/load_revm_nif_test.erl)
* ao process example: [evm-device.lua](https://github.com/loadnetwork/load_hb/blob/main/test/evm-device.lua)
