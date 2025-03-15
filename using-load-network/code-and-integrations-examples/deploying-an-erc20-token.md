---
description: Deploy an ERC20 token on Load Network
---

# Deploying an ERC20 Token

### **Add Load Network Alphanet to MetaMask**

Before deploying, make sure the Load Network network is configured in your MetaMask wallet. [Check the Network Configurations](../network-configurations.md).

### ERC20 Contract

For this example, we will use the ERC20 token template provided by the [OpenZeppelin's](https://docs.openzeppelin.com/contracts/4.x/erc20) smart contract library.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

/// @title Useless Testing Token
/// @notice Just a testing shitcoin
/// @dev SupLoad gmgm
/// @author pepe frog
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract WeaveGM is ERC20 {
    constructor(uint256 initialSupply) ERC20("supLoad", "LOAD") {
        _mint(msg.sender, initialSupply);
    }
}
```

### Deployment

Now that you have your contract source code ready, compile the contract and hit deploy with an initial supply.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption><p>69420 LOADs because why not</p></figcaption></figure>

After deploying the contract successfully, check your EOA balance!

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption><p>Success!</p></figcaption></figure>
