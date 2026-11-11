> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Data Retention & Storage

> Understand app-owner responsibilities for customer content in Serverless logs, persistent storage, and media uploads.

As a Serverless app owner, you control what your application stores and logs. Review every place your code and its dependencies write customer content, including prompts, uploaded media, and generated outputs.

If you only call hosted model endpoints, see [Model API Data Retention & Storage](../model-apis/media-expiration.md) for the controls available to API callers.

## Who Controls Retention?

| Storage path | Responsible party |
| - | - |
| **Request-history payloads** | fal applies the caller's `X-Fal-Store-IO` header. No app-side handler is needed. |
| **Application logs** | The app owner controls what the app and its dependencies emit. |
| **Persistent storage** | The app owner controls writes and cleanup in `/data`, KVStore, and any external stores used by the app. |
| **CDN media** | The app owner uses toolkit uploads to apply the caller's expiration and access preferences to generated files. |

## Request Payloads

fal keeps request inputs and outputs (JSON) for **90 days** by default to power request history. Callers can send `X-Fal-Store-IO: 0` to disable this storage for a request. They can also ask their fal account team to disable it by default for the account. Explicit per-request headers take precedence over the account default. fal handles this setting, so your app code does not need to change. See [Model API Data Retention & Storage](../model-apis/media-expiration.md#request-payloads) for details.

## Content-Bearing Logs

fal keeps your app's `stdout` and `stderr` output for **30 days**, including `print()` calls, Python logging, and error tracebacks. For ZDR workloads, make sure your code and its dependencies do not log prompts, inputs, or outputs. Check exception messages and debug output as well as intentional application logs.

Application logs are owner-only. Non-owner callers receive no request logs, even for requests they submitted. This access restriction does not disable log capture or delete stored logs. Neither `private_logs` nor the account's Log Privacy setting is a retention control.

See [Logging](../development/logging.md) for how logs are captured and exposed.

## Persistent Storage

Files written to the shared [`/data` volume](../development/use-persistent-storage.md) persist across requests, runner restarts, and deployments until you delete them. As the app owner, manage which files are written and when they are removed. Runner shutdown does not delete these files.

[KVStore](../development/use-kv-store.md) values expire after at most **30 days**, which is also the default. To remove values sooner, set a shorter `ttl` or delete them explicitly.

For ZDR workloads, keep customer content out of both `/data` and KVStore, including caches and intermediate files. If your app uses an external database or object store, manage that store's retention separately.

## Uploaded and Generated Media

Return generated files through toolkit types such as `fal.toolkit.Image` and `fal.toolkit.File` to apply the caller's `X-Fal-Object-Lifecycle-Preference`, including expiration settings and `initial_acl`, to CDN uploads. See [Controlling Access to Returned Files](../development/working-with-files.md#controlling-access-to-returned-files) for implementation patterns. If your app enforces its own output ACL, preserve the caller's expiration settings as shown there.

fal passes `X-Fal-CDN-Token` to your runner for CDN downloads and uploads on behalf of the API caller. Toolkit uploads use the caller's CDN token automatically, so generated files belong to the caller. For custom uploads, see [ACLs Inside a Serverless App](../model-apis/file-access-controls.md#acls-inside-a-serverless-app).

Streaming bytes directly to the caller does not upload them to the CDN. Files that your streaming code uploads, for example through toolkit types, are still stored on the CDN under the normal CDN retention settings.

## Operational Metadata

Disabling request-history payload storage does not disable operational metadata, such as request IDs, endpoints, status, timestamps, billing, usage, and latency. These records are separate from the customer content in request payloads, logs, and files described above.

## Zero Data Retention (ZDR)

For Serverless workloads with ZDR requirements:

* Have callers disable request-history storage, per request or through their account default.
* Avoid retaining customer content in application logs and persistent storage.
* Honor the caller's CDN expiration and access preferences.

[Contact the sales team](https://fal.ai/enterprise#contact-sales) to confirm ZDR availability and coverage for your deployment. The fal account team configures the [account-level storage defaults](../model-apis/media-expiration.md#zero-data-retention-zdr) for calling accounts. These defaults do not replace reviewing your app's logging and storage behavior.
