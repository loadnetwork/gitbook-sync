---
description: Permanent EigenDA blobs
---

# LN-EigenDA Proxy Server

### Links

EigenDA proxy: [repository](https://github.com/weaveVM/wvm-eigenda-proxy/tree/feat/eigenda-wvm-code-integration)

### About EigenDA Side Server Proxy

LN-EigenDA wraps the [high-level EigenDA client](https://github.com/Layr-Labs/eigenda/blob/master/api/clients/eigenda_client.go), exposing endpoints for interacting with the EigenDA disperser in conformance to the [OP Alt-DA server spec](https://specs.optimism.io/experimental/alt-da.html), and adding disperser verification logic. This simplifies integrating EigenDA into various rollup frameworks by minimizing the footprint of changes needed within their respective services.

### About LN-EigenDA Side Server Proxy Integration

It's a Load Network integration as a secondary backend of eigenda-proxy. In this scope, Load Network provides an EVM gateway/interface for EigenDA blobs on Arweave's Permaweb, removing the need for trust assumptions and relying on centralized third party services to sync historical data and provides a "pay once, save forever" data storage feature for EigenDA blobs.

#### Key Details

* Current maximum encoded blob size is 8 MiB (8\_388\_608 bytes).
* _**Load Network currently operating in public testnet (Alphanet) - not recommended to use it in production environment.**_

#### Prerequisites and Resources

1. Review the configuration parameters table and `.env` file settings for the Holesky network.
2. Obtain test tLOAD tokens through our [faucet](https://wvm.dev/faucet) for testing purposes.
3. Monitor your transactions using the [Load Network explorer.](https://explorer.load.network)

### Usage Examples

Please double check .env file values you start eigenda-proxy binary with env vars.\
They may conflict with flags.

\
Start eigenda proxy with LN private key:

```
./bin/eigenda-proxy \
    --addr 127.0.0.1 \
    --port 3100 \
    --eigenda.disperser-rpc disperser-holesky.eigenda.xyz:443 \
    --eigenda.signer-private-key-hex $PRIVATE_KEY \
    --eigenda.max-blob-length 8Mb \
    --eigenda.eth-rpc https://ethereum-holesky-rpc.publicnode.com \
    --eigenda.svc-manager-addr 0xD4A7E1Bd8015057293f0D0A557088c286942e84b \
    --weavevm.endpoint https://alphanet.load.network/ \
    --weavevm.chain_id 9496 \
    --weavevm.enabled \
    --weavevm.private_key_hex $WVM_PRIV_KEY \
    --storage.fallback-targets weavevm \
    --storage.concurrent-write-routines 2 
```

POST command:

```
curl -X POST "http://127.0.0.1:3100/put?commitment_mode=simple" \
      --data-binary "some data that will successfully be written to EigenDA" \
      -H "Content-Type: application/octet-stream" \
      --output response.bin
```

\
GET command:

```
COMMITMENT=$(xxd -p response.bin | tr -d '\n' | tr -d ' ')
curl -X GET "http:/127.0.0.1:3100/get/0x$COMMITMENT?commitment_mode=simple" \
     -H "Content-Type: application/octet-stream"
```



## Examples using Web3signer as a remote signer

### Web3 signer

[Web3Signer](https://docs.web3signer.consensys.net/en/latest/) is a tool by Consensys which allows _remote signing_.&#x20;

### Warnings <a href="#warnings" id="warnings"></a>

Using a remote signer comes with risks, please read the web3signer docs. However this is a recommended way to sign transactions for enterprise users and production environments.\
Web3Signer is not maintained by Load Network team.\
\
Example of the most simple local web3signer deployment (for testing purposes): [https://github.com/allnil/web3signer\_test\_deploy](https://github.com/allnil/web3signer_test_deploy)&#x20;

start eigenda proxy with signer:

```
./bin/eigenda-proxy \
    --addr 127.0.0.1 \
    --port 3100 \
    --eigenda.disperser-rpc disperser-holesky.eigenda.xyz:443 \
    --eigenda.signer-private-key-hex $PRIVATE_KEY \
    --eigenda.max-blob-length 8MiB \
    --eigenda.eth-rpc https://ethereum-holesky-rpc.publicnode.com \
    --eigenda.svc-manager-addr 0xD4A7E1Bd8015057293f0D0A557088c286942e84b \
    --weavevm.endpoint https://alphanet.load.network/ \
    --weavevm.chain_id 9496 \
    --weavevm.enabled \
    --weavevm.web3_signer_endpoint http://localhost:9000 \
    --storage.fallback-targets weavevm \
    --storage.concurrent-write-routines 2 
```

start web3signer tls:

```
./bin/eigenda-proxy \
    --addr 127.0.0.1 \
    --port 3100 \
    --eigenda.disperser-rpc disperser-holesky.eigenda.xyz:443 \
    --eigenda.signer-private-key-hex $PRIVATE_KEY \
    --eigenda.max-blob-length 8MiB \
    --eigenda.eth-rpc https://ethereum-holesky-rpc.publicnode.com \
    --eigenda.svc-manager-addr 0xD4A7E1Bd8015057293f0D0A557088c286942e84b \
    --weavevm.endpoint https://testnet-rpc.wvm.dev/ \
    --weavevm.chain_id 9496 \
    --weavevm.enabled \
    --weavevm.web3_signer_endpoint https://localhost:9000 \
    --storage.fallback-targets weavevm \
    --storage.concurrent-write-routines 2 \
    --weavevm.web3_signer_tls_cert_file $SOME_PATH_TO_CERT \
    --weavevm.web3_signer_tls_key_file $SOME_PATH_TO_KEY \
    --weavevm.web3_signer_tls_ca_cert_file $SOME_PATH_TO_CA_CERT
```
