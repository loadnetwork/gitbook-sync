---
description: >-
  Description of Laod Network integration as a Data Availability client for
  Dymension RollApps
---

# LN-Dymension: DA client for RollAP

#### Links

[https://dymension.xyz](https://dymension.xyz)

{% embed url="https://github.com/dymensionxyz/rollapp-evm" %}

{% embed url="https://github.com/dymensionxyz/dymint/" %}

#### Key Details

* Load Network provides a gateway for Arweave's permanent with its own (LN) high data throughput of the permanently stored data into .
* Current maximum encoded blob size is 8 MB (8\_388\_608 bytes).
* _**Laod Network currently operating in public testnet (Alphanet) - not recommended to use it in production environment.**_

#### Prerequisites and Resources

1. Understand how to boot basic Dymension RollApp and how to configure it.
2. Obtain test tLOAD tokens through our [faucet](https://wvm.dev/faucet) for testing purposes.
3. Monitor your transactions using the [Load Network explorer](https://explorer.load.network).

_**How it works**_

You may choose to use Load Network as a DataAvailability layer of your RollApp. We assume that you know how to boot and configure basics of your dymint RollApp. As an example you may use \
[https://github.com/dymensionxyz/rollapp-evm](https://github.com/dymensionxyz/rollapp-evm) repository. \
\
Example uses "mock" DA client. To use Load Network you should simply set next environment variable \
before config generation step using init.sh\
`export DA_CLIENT="weavevm" # This is the key change`\
`export WVM_PRIV_KEY="your_hex_string_wvm_priv_key_without_0x_prefix"`\
\
init.sh will generate basic configuration for `da_config.json` in `dymint.toml` which should look like.\
\
`da_config = '{"endpoint":"https://alphanet.load.network","chain_id":9496,"timeout":60000000000,"private_key_hex":"your_hex_string_load_priv_key_without_0x_prefix"}'`\
\
In this example we use `PRIVATE_KEY` of your LN address. It's not the most secure way to handle transaction signing and that's why we also provide an ability to use web3signer as a signing method.  To enable web3signer you will need to change init.sh script and add correspondent fields or change `da_config.json` in `dymint.toml` directly. \
e.g\
\
`da_config = '{"endpoint":"https://alphanet.load.network","chain_id":9496,"timeout":"60000000000","web3_signer_endpoint":"http://localhost:9000"}'`\


and to enable tls next fields should be add to the json file:

`web3_signer_tls_cert_file`\
`web3_signer_tls_key_file`\
`web3_signer_tls_ca_cert_file`\
\
\
Web3 signer

[Web3Signer](https://docs.web3signer.consensys.net/en/latest/) is a tool by Consensys which allows _remote signing_.&#x20;

### Warnings <a href="#warnings" id="warnings"></a>

Using a remote signer comes with risks, please read the web3signer docs. However this is a recommended way to sign transactions for enterprise users and production environments.\
Web3Signer is not maintained by Load Network team.\
\
Example of the most simple local web3signer deployment (for testing purposes): [https://github.com/allnil/web3signer\_test\_deploy](https://github.com/allnil/web3signer_test_deploy) \
&#x20;\
Example of used configuration:&#x20;

```
# Set environment variables
export DA_CLIENT="weavevm"  # This is the key change
export WVM_PRIV_KEY="your_hex_string_wvm_priv_key_without_0x_prefix"

export ROLLAPP_CHAIN_ID="rollappevm_1234-1"
export KEY_NAME_ROLLAPP="rol-user"
export BASE_DENOM="arax"
export MONIKER="$ROLLAPP_CHAIN_ID-sequencer"
export ROLLAPP_HOME_DIR="$HOME/.rollapp_evm"
export SETTLEMENT_LAYER="mock"

# Initialize and start
make install BECH32_PREFIX=$BECH32_PREFIX
export EXECUTABLE="rollapp-evm"
$EXECUTABLE config keyring-backend test

sh scripts/init.sh

# Verify dymint.toml configuration
cat $ROLLAPP_HOME_DIR/config/dymint.toml | grep -A 5 "da_config"

dasel put -f "${ROLLAPP_HOME_DIR}"/config/dymint.toml "max_idle_time" -v "2s"
dasel put -f "${ROLLAPP_HOME_DIR}"/config/dymint.toml "max_proof_time" -v "1s"
dasel put -f "${ROLLAPP_HOME_DIR}"/config/dymint.toml "batch_submit_time" -v "30s"
dasel put -f "${ROLLAPP_HOME_DIR}"/config/dymint.toml "p2p_listen_address" -v "/ip4/0.0.0.0/tcp/36656"
dasel put -f "${ROLLAPP_HOME_DIR}"/config/dymint.toml "settlement_layer" -v "mock"
dasel put -f "${ROLLAPP_HOME_DIR}"/config/dymint.toml "node_address" -v "http://localhost:36657"
dasel put -f "${ROLLAPP_HOME_DIR}"/config/dymint.toml "settlement_node_address" -v "http://127.0.0.1:36657"


# Start the rollapp

$EXECUTABLE start --log_level=debug \
  --rpc.laddr="tcp://127.0.0.1:36657" \
  --p2p.laddr="tcp://0.0.0.0:36656" \
  --proxy_app="tcp://127.0.0.1:36658"

```

\


in rollap-evm log you will eventually see something like this:

```log
INFO[0000] weaveVM: successfully sent transaction[tx hash 0x8a7a7f965019cf9d2cc5a3d01ee99d56ccd38977edc636cc0bbd0af5d2383d2a]  module=weavevm
INFO[0000] wvm tx hash[hash 0x8a7a7f965019cf9d2cc5a3d01ee99d56ccd38977edc636cc0bbd0af5d2383d2a]  module=weavevm
DEBU[0000] waiting for receipt[txHash 0x8a7a7f965019cf9d2cc5a3d01ee99d56ccd38977edc636cc0bbd0af5d2383d2a attempt 0 error get receipt failed: failed to get transaction receipt: not found]  module=weavevm
INFO[0002] Block created.[height 35 num_tx 0 size 786]   module=block_manager
DEBU[0002] Applying block[height 35 source produced]     module=block_manager
DEBU[0002] block-sync advertise block[error failed to find any peer in table]  module=p2p
INFO[0002] MINUTE EPOCH 6[]                              module=x/epochs
INFO[0002] Epoch Start Time: 2025-01-13 09:21:03.239539 +0000 UTC[]  module=x/epochs
INFO[0002] commit synced[commit 436F6D6D697449447B5B3130342038203131302032303620352031323920393020343520313633203933203235322031352031343320333920313538203131342035382035352031352038322038203939203132392032333520313731203230382031392032343320313932203139203233352036355D3A32337D]
DEBU[0002] snapshot is skipped[height 35]
INFO[0002] Gossipping block[height 35]                   module=block_manager
DEBU[0002] Gossiping block.[len 792]                     module=p2p
DEBU[0002] indexed block[height 35]                      module=txindex
DEBU[0002] indexed block txs[height 35 num_txs 0]        module=txindex
INFO[0002] Produced empty block.[]                       module=block_manager
DEBU[0002] Added bytes produced to bytes pending submission counter.[bytes added 786 pending 15719]  module=block_manager
INFO[0003] data available in weavevm[wvm_tx 0x8a7a7f965019cf9d2cc5a3d01ee99d56ccd38977edc636cc0bbd0af5d2383d2a wvm_block 0xe897eab56aee50b97a0f2bd1ff47af3c834e96ca18528bb869c4eafc0df583be wvm_block_number 5651207]  module=weavevm
DEBU[0003] Submitted blob to DA successfully.[]          module=weavevm
```

