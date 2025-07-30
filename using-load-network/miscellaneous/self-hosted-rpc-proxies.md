# Self Hosted RPC Proxies

## Rust Proxy

### Run Locally

```bash
git clone https://github.com/weavevm/wvm-proxy-rpc.git

cd wvm-proxy-rpc

cargo build && cargo shuttle run --port 3000
```

### Try it!

```bash
curl -X POST http://localhost:3000 -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"eth_chainId","params":[],"id":1}'
```

You can find the proxy server codebase here: [https://github.com/weaveVM/wvm-rpc-proxy](https://github.com/weaveVM/wvm-rpc-proxy)

## JavaScript Proxy

### Run Locally

```bash
git clone https://github.com/weavevm/proxy-rpc.git

cd proxy-rpc

npm install && npm run start
```

### Try it!

```bash
curl -X POST http://localhost:3000 -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"eth_chainId","params":[],"id":1}'
```

You can find the proxy server codebase here: [https://github.com/weaveVM/proxy-rpc](https://github.com/weaveVM/proxy-rpc)
