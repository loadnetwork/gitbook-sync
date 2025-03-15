---
description: Run a JavaScript RPC Proxy locally or on cloud
---

# JavaScript Proxy

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
