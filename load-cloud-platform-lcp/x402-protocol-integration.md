---
description: The first ever temporary, private, payment-gated ANS-104 Dataitems
---

# x402 Protocol Integration

### What is the x402 protocol?

The x402 protocol is an open framework that enables machine-to-machine payments on the web by standardizing how clients and services exchange value over HTTP. Building on the HTTP 402 "Payment Required" status code, x402 establishes a clear transaction flow where a server responds to resource requests with payment instructions (including amount and recipient), the client provides payment authorization, a payment facilitator verifies and settles the transaction, and the server delivers the requested resource along with payment confirmation.&#x20;

This protocol allows automated agents, crawlers, and digital services to conduct transactions programmatically without requiring traditional accounts, subscriptions, or API keys. By creating a common language for web-based payments, x402 enables new monetization models such as pay-per-use access, micropayments for AI agents purchasing from multiple merchants, and flexible payment schemes including immediate settlement via stablecoins or deferred settlement through traditional payment rails like credit cards and bank accounts.

{% hint style="info" %}
[The x402 Foundation](https://blog.cloudflare.com/x402/) is a collaborative initiative being established by Cloudflare and Coinbase with the mission of encouraging widespread adoption of the x402 protocol.
{% endhint %}

### x402 and xANS-104 DataItems

We are proud to be the first team that has worked on the intersection of x402 micropayments protocol and Arweave's ANS-104 data format. We have integrated the x402 protocol in Load's custom HyperBEAM device ([\~s3@1.0 device](../load-hyperbeam/s3-1.0-device.md)), resulting in the first ever expirable, paywalled, privately shareable ANS-104 DataItems.

The x402 micropayments have been integrated on the ANS-104 gateway sidecar level of the s3 device. [check out source code](https://app.gitbook.com/u/9f5jQHFG1jWXf1Txd6jCIxFWKHD2).

### Creating x402 paywalled private dataitems

There are two ways to create an x402 paywalled private expireable ANS-104 DataItem, the first is user friendly via the LCP dashboard, and the second is programmatic yet DIY, let's explore both methods.

#### x402 on Load Cloud Platform (LCP)

x402 has been integrated on LCP, making it possible for LCP users to create x402 paywalled, expirable links for their private objects (ANS-104 DataItems) on Load S3. The process is pretty simple and straightforward: fill in the parameters of the x402 request (set expiry , payee EOA, and USDC amount) and the dashboard will generate a ready-to-use x402 expirable DataItem URL.

\
\[screenshot here]\


#### x402 DIY

#### &#x20; 

