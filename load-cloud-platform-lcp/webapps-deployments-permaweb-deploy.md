---
description: How to use Arweave's permaweb-deploy with preview temporary deployments
---

# WebApps Deployments (permaweb-deploy)

{% hint style="info" %}
Inspired by the [cookbook github action deployment guide](https://cookbook.arweave.dev/guides/deployment/github-action.html), `permaweb-deploy` is a Node.js command-line tool designed to streamline the deployment of web applications to the permaweb using Arweave. It uploads your build folder or a single file, creates Arweave manifests, and updates ArNS (Arweave Name Service) records via ANT (Arweave Name Token) with the transaction ID. [source](https://github.com/permaweb/permaweb-deploy)
{% endhint %}

We have forked the original `permaweb-deploy` CLI to add support for Load S3 as an alternative upload option (through [loaded-turbo-api](turbo-offchain-upload-service.md)) beside the default Turbo-Arweave permanent uploads. &#x20;

### Preview Feature in Details

the loadnetwork permaweb-deploy forked CLI have the `--preview` feature flag, where if used, the web app (UI) is deployed on Load S3 as [xANS-104](https://blog.load.network/xans-104) dataitem instead of having the permanent-only deployment option.

This feature unlocks missing features in permaweb deployment workflow: staging deployments. With this feature, you can deploy as you work with Vercel (dev UX), stage deployment as `preview`, promote to `prod` only when it is ok to commit the webapp ui permanently on Arweave.

{% hint style="info" %}
permaweb-deploy's --preview feature uses xANS-104 dataitems under the hood, enabling offchain temporary dataitems for staging deployments; however, thanks to xANS-104, migrating the temporary Arweave Manifest to Arweave (permanent) is a single anchoring click that maintains determinism and provenance. &#x20;
{% endhint %}

### Dev Guide

First, clone the repository locally: [https://github.com/loadnetwork/permaweb-deploy](https://github.com/loadnetwork/permaweb-deploy)

#### **setup**

```bash
pnpm install
pnpm build
pnpm link --global
```

#### **deploy**

from your ui folder:

```bash
permaweb-deploy deploy --preview --deploy-folder ./dist --wallet ./wallet.json
```

if you want to avoid linking, run it directly from the repo:

```bash
node ./bin/run.js deploy --preview --deploy-folder /path-to-dist/dist --wallet /path-to-wallet/wallet.json
```

Example response:

```
🚀 Starting deployment...

✔ Skipping ANT update
✔ Signer created (arweave)
✔ Turbo initialized
✔ Folder uploaded: Uk-b10wtSSVShM3dg64XzaP9TOusnMsHirpWemb8b98

Uploaded successfully: Uk-b10wtSSVShM3dg64XzaP9TOusnMsHirpWemb8b98
Visit your site: https://gateway.s3-node-1.load.network/resolve/preview/Uk-b10wtSSVShM3dg64XzaP9TOusnMsHirpWemb8b98
```
