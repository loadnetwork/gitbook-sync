---
description: Use Load Network with etherjs
---

# ethers (etherjs)

In this example we will use the [ethers npm package.](https://docs.ethers.org/) First of all, install the package:

```bash
npm i ethers
```

### Code Example: Retrieve Address Balance

```javascript
import { ethers } from "ethers";

const provider = new ethers.providers.JsonRpcProvider("https://alphanet.load.network");
const address = "0x544836c1d127B0d5ed6586EAb297947dE7e38a78";

async function getBalance() {
    const balance = await provider.getBalance(address);
    console.log(`Balance: ${ethers.utils.formatEther(balance)} tLOAD`);
}

getBalance();

```
