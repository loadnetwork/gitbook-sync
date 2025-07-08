---
description: 'The Load Network Gateway Stack: Fast, Reliable Access to Load Network Data'
---

# Bundlers Gateways

All storage chains have the same issue: even if the data storage is decentralized, retrieval is handled by a centralized gateway. A solution to this problem is just to provide a way for anyone to easily run their own gateway – and if you’re an application building on Load Network, that’s a great way to ensure content is rapidly retrievable from the blockchain.

When [relic.bot](https://relic.bot) – a photo sharing dApp that uses LN bundles for storage – started getting traction, the default LN gateway became a bottleneck for the Relic team. The way data is stored inside bundles (hex-encoded, serialized, compressed) can make it resource-intensive to decode and present media on demand, especially when thousands of users are doing so in parallel.

In response, we developed two new open source gateways: one [JavaScript-based cache-enabled gateway](https://github.com/weaveVM/resolver.bot), and [one written in Rust](https://github.com/weaveVM/rusty-gateway/tree/main).

The LN Gateway Stack introduces a powerful new way to access data from Load Network bundles, combining high performance with network resilience. At its core, it’s designed to make bundle data instantly accessible while contributing to the overall health and decentralization of the LN.

### **Why we built the Load Network gateway stack**

The gateway stack solves several critical needs in the LN ecosystem:

**Rapid data retrieval**

Through local caching with SQLite, the gateway dramatically reduces load times (4-5x) for frequently accessed bundled data. No more waiting for remote data fetches – popular content is served instantly from the gateway node.

For [relic.bot](http://relic.bot/), this slashed feed loading times from 6-8 seconds to near-instant.

**Network health**

By making it easy to run your own gateway, the stack promotes a more decentralized network. Each gateway instance contributes to network redundancy, ensuring data remains accessible even if some nodes go offline.

### **Running a Load Network gateway**

Running your own LN gateway is pretty straightforward. The gateway stack is designed for easy deployment, directly to your server or inside a Docker container.

With Docker, you can have a gateway up and running in minutes:

```bash
git clone https://github.com/weavevm/bundles-gateway.git  
cd bundles-gateway  
docker compose up -d
```

For rustaceans, rusty-gateway is deployable on a Rust host like [shuttle.dev](http://shuttle.dev/) – get the repo [here](https://github.com/WeaveVM/rusty-gateway) and Shuttle deployment docs [here](https://docs.shuttle.dev/introduction/docs).

### **The technical side**

Under the hood, the gateway stack features:

* SQLite-backed persistent cache
  * Content-aware caching with automatic MIME type detection
  * Configurable cache sizes and retention policies
  * Application-specific cache management
  * Automatic cache cleanup based on age and size limits
  * Health monitoring and statistics

The gateway exposes a simple API for accessing bundle data:

`GET /bundle/:txHash/:index`

This endpoint handles the job of data retrieval, caching, and content-type detection behind the scenes.

### **Towards scalability & decentralization**

The Load Network gateway stack was built in response to problems of scale – great problems to have as a new network gaining traction. LN bundle data is now more accessible, resilient and performant. By running a gateway, you’re not just improving your own access to LN data – you’re contributing to a more robust, decentralized network.

Test the gateways:

* [gateway.wvm.rs](http://gateway.wvm.rs/) - [gateway.load.rs](https://gateway.load.rs)
* [gateway.wvm.nerwork](https://gateway.wvm.nerwork)
* [resolver.bot](http://resolver.bot/)
