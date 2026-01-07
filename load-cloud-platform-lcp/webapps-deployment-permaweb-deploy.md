---
description: How to use Arweave's permaweb-deploy with preview temporary deployments
---

# WebApps Deployment (permaweb-deploy)

{% hint style="info" %}
Inspired by the [cookbook github action deployment guide](https://cookbook.arweave.dev/guides/deployment/github-action.html), `permaweb-deploy` is a Node.js command-line tool designed to streamline the deployment of web applications to the permaweb using Arweave. It uploads your build folder or a single file, creates Arweave manifests, and updates ArNS (Arweave Name Service) records via ANT (Arweave Name Token) with the transaction ID. [source](https://github.com/permaweb/permaweb-deploy)
{% endhint %}

We have forked the original `permaweb-deploy` CLI to add support for Load S3 as an alternative upload option (through [loaded-turbo-api](turbo-offchain-upload-service.md)) beside the default Turbo-Arweave permanent uploads. &#x20;

### Preview Feature in Details

the loadnetwork's `permaweb-deploy` forked CLI have the `--preview` feature flag, where if used, the web app (UI) is deployed on Load S3 as [xANS-104](https://blog.load.network/xans-104) dataitem instead of having the permanent-only deployment option.

This feature unlocks missing features in permaweb deployment workflow: staging deployments. With this feature, you can deploy as you work with Vercel (dev UX), stage deployment as `preview`, promote to `prod` only when it is ok to commit the webapp ui permanently on Arweave.

{% hint style="info" %}
permaweb-deploy's --preview feature uses xANS-104 dataitems under the hood, enabling offchain temporary dataitems for staging deployments; however, thanks to xANS-104, migrating the temporary Arweave Manifest to Arweave (permanent) is a single anchoring click that maintains determinism and provenance. &#x20;
{% endhint %}

### Dev Guide

#### **option 1: npmjs package install**

```bash
npm i -g @loadnetwork/permaweb-deploy
```

#### option 2: dev install from source

```bash
git clone https://github.com/loadnetwork/permaweb-deploy
cd permaweb-deploy

pnpm install
pnpm build
pnpm link --global
```

{% hint style="info" %}
To use the `@loadnetwork/permaweb-deploy` CLI, you can use either `load-permaweb-deploy` or the fork's canonical `permaweb-deploy` cli name. to avoid conflicts, we advise using `load-permaweb-dpeloy` for load's fork, as in the examples below.
{% endhint %}

#### **deploy**

from your ui folder:

```bash
load-permaweb-deploy deploy --preview --deploy-folder ./dist --wallet ./wallet.json
```

\[dev] if you want to avoid linking, run it directly from the repo:

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

Additionally you can access the Arweave Manifest JSON's raw payload using the `/resolve/{dataitem_id}` endpoint; example: [https://gateway.s3-node-1.load.network/resolve/Uk-b10wtSSVShM3dg64XzaP9TOusnMsHirpWemb8b98](https://gateway.s3-node-1.load.network/resolve/Uk-b10wtSSVShM3dg64XzaP9TOusnMsHirpWemb8b98)

#### anchor

to anchor load s3 preview (staging) deployment to Arweave while maintaining determinism and provenance:

```bash
load-permaweb-deploy deploy --anchor --preview-id <manifest-id> --wallet ./wallet.json
```

to access deployments, either staging (temporary) or prod (arweave-settled), check the [LS3 Dataitems gateways page](load-s3-dataitems-gateway.md)
