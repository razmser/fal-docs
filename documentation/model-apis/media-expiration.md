> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Data Retention & Storage

> Control retention of Model API request payloads and media, and confirm Zero Data Retention (ZDR) coverage.

When you call a Model API, your inputs and outputs can be stored in [Dashboard > Recent History](https://fal.ai/dashboard), and uploaded and generated media can be stored on the [fal CDN](fal-cdn.md). This page explains the retention controls available to you as an API caller.

If you deploy your own apps, see [Serverless Data Retention & Storage](../serverless/data-retention.md) for your responsibilities as an app owner, including application logs and persistent storage.

## Zero Data Retention (ZDR)

For inference workloads, fal supports Zero Data Retention (ZDR) configurations involving prompts, uploaded media, generated outputs, and content-bearing logs. If you require no retention after processing, [contact the sales team](https://fal.ai/enterprise#contact-sales) to confirm ZDR availability and coverage for your endpoints.

ZDR is not enabled by default. Disabling request payload storage is one part of a ZDR configuration. It does not disable every place where content can be stored.

ZDR is not a single setting. Your fal account team can set two separate account defaults for you:

* Disable request-history payload storage.
* Set a default expiration for uploaded and generated files on the CDN.

Neither default disables application logging or application-managed persistent storage.

| Content | What you can do as an API caller |
| - | - |
| **Prompts, inputs and outputs** | Disable request-history payload storage with `X-Fal-Store-IO: 0`, or ask your account team to disable it by default for your account. Explicit per-request headers take precedence over the account default. |
| **Uploaded media and generated files** | CDN storage is separate from request payload storage. Configure `X-Fal-Object-Lifecycle-Preference` to control how long uploaded and generated files are retained. Alternatively, ask your fal account team to configure the account default. Per-request headers take precedence. |

The model deployment owner controls application logging and application-managed persistent storage. The request-history and CDN headers do not configure these deployment behaviors. If a model is not marked Enterprise-ready, [contact us](https://fal.ai/enterprise#contact-sales) before using it for ZDR workloads.

## Generated Media (CDN Files)

When a model generates output files, they are uploaded to the [fal CDN](fal-cdn.md) and returned as URLs (e.g., `https://v3b.fal.media/files/...`). Files you upload as inputs via `fal_client.upload_file` are also stored on the CDN. Both input uploads and output media are subject to the same retention controls.

You can ask your fal account team to configure a default CDN expiration for your account. The per-request header takes precedence over this default.

### Per-Request Expiration

Control how long generated media is stored using the `X-Fal-Object-Lifecycle-Preference` header:

```python Python theme={null}
import fal_client
import json

result = fal_client.subscribe(
    "fal-ai/flux/schnell",
    arguments={"prompt": "a sunset"},
    headers={
        "X-Fal-Object-Lifecycle-Preference": json.dumps({
            "expiration_duration_seconds": 3600
        })
    }
)
```

```javascript JavaScript theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/flux/schnell", {
  input: { prompt: "a sunset" },
  headers: {
    "X-Fal-Object-Lifecycle-Preference": JSON.stringify({
      expiration_duration_seconds: 3600
    })
  }
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/flux/schnell" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -H 'X-Fal-Object-Lifecycle-Preference: {"expiration_duration_seconds": 3600}' \
  -d '{"prompt": "a sunset"}'
```

Set `expiration_duration_seconds` to the number of seconds, or `null` for no expiration.

> [!WARNING]
>
> Expired files are permanently deleted and cannot be recovered. Download files before they expire if you need them later.

> [!NOTE]
>
> The same `X-Fal-Object-Lifecycle-Preference` header also accepts an `initial_acl` field that controls **who** can access the generated files, in addition to how long they live. See [File Access Controls](file-access-controls.md) for the full ACL shape and examples that combine retention with access rules.

***

## Request Payloads

Request inputs and outputs are stored in the platform for **90 days** by default. This powers the request history in your dashboard.

### Preventing Payload Storage

To prevent fal from retaining inputs and outputs in request history, add the `X-Fal-Store-IO: 0` header:

```python Python theme={null}
result = fal_client.subscribe(
    "fal-ai/flux/schnell",
    arguments={"prompt": "a sunset"},
    headers={"X-Fal-Store-IO": "0"}
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/flux/schnell", {
  input: { prompt: "a sunset" },
  headers: { "X-Fal-Store-IO": "0" }
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/flux/schnell" \
  -H "Authorization: Key $FAL_KEY" \
  -H "X-Fal-Store-IO: 0" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset"}'
```

> [!NOTE]
>
> This controls request-history payload storage only. CDN files have separate retention controls. The model deployment owner controls application logging. See [Zero Data Retention (ZDR)](#zero-data-retention-zdr) for the full scope to consider.

### Deleting Payloads

You can delete a request's payloads and the CDN files in its output via the [Platform API](../../api-reference/platform-apis.md):

> [card] [Delete Request Payloads API](../../platform-apis/v1/models/requests/payloads.md)
>
> Delete IO payloads and CDN output files for a specific request

> [!WARNING]
>
> **Requests must be completed before deletion:** You cannot delete payloads for a request that is still in progress. The API returns a `400` error if the request has not yet finished. Retry once the request is complete.

> [!NOTE]
>
> CDN files found in the **input** of a request are not deleted, as they may be used by other requests. To delete individual input files, use the [CDN file deletion endpoint](fal-cdn.md#deleting-files).

***

## Summary

| Data Type | Default Retention | Control |
| - | - | - |
| **Uploaded and generated media** (CDN files) | Configurable | Account default, `X-Fal-Object-Lifecycle-Preference` header, or explicit deletion. The header also controls [File ACLs](file-access-controls.md). |
| **Request payloads** | 90 days | `X-Fal-Store-IO: 0` header to opt out, account default configured by your account team, or delete via API |
