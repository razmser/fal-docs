> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../quickstart.md) and the [Model APIs overview](../../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Error Analytics

> Explore, filter, and debug request errors across your applications.

The Error Analytics dashboard gives you a centralized view of all request errors across your deployed applications. Use it to spot error spikes, identify which endpoints or status codes are causing the most failures, and drill into individual failed requests to see full logs and timing.

You can access error analytics per-app (from any app's **Errors** tab) or globally across all apps at [**Dashboard > Apps > Errors**](https://fal.ai/dashboard/apps/errors). Filter by endpoint, status code, and date range to narrow down the issue.

## Debugging Errors

When you see errors in the dashboard, click into any individual failed request to see the full details: when it was submitted, how long it took, what status code was returned, and the complete [request logs](../../development/logging.md). This is the fastest way to go from "something is failing" to "here's the stack trace."

For errors that correlate with a deployment, check [App Events](app-events.md) to see if a recent deploy or config change caused the issue. If the error rate is tied to a specific status code, see [Retries and Error Handling](../reliability/retries.md) to understand what each code means for your runners and retries.

## Common Error Patterns

| Status | What it usually means |
| - | - |
| **500** | Unhandled exception in your endpoint code. Runner stays alive. |
| **502** | Runner crashed or connection was lost during processing. |
| **503** | Runner is in a bad state and was terminated. Request is retried. |
| **504** | Request exceeded timeout. Often from cold starts or heavy workloads. |
| **429** | Concurrency limit reached. Request is re-queued automatically. |

For a complete reference on how each status code affects runners, retries, and billing, see [Status Codes](../reliability/retries.md#status-code-reference).

> [card] [Status Codes](../reliability/retries.md#status-code-reference)
>
> How each HTTP status code affects runners and retries

> [card] [Retries](../reliability/retries.md)
>
> How fal automatically retries failed requests
