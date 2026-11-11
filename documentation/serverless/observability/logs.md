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

# Logs

> Explore, filter, share, and export your application logs from the dashboard.

The Logs page is the dashboard viewer for everything your app writes to `stdout` and `stderr`. It is built to cut through noise quickly: a timeline to spot error clusters, drag-to-filter to zoom into a window, and collaboration features so you and your team (or the fal team) can share and debug the same log line. For how logging works under the hood -- the different log scopes, what is captured, and how request-level logs are exposed -- see [Logging](../../development/logging.md).

## Timeline and Filtering

The logs viewer opens with a timeline histogram of log volume over time, making it easy to see when errors spiked or clustered. From there you can:

* **Drag to filter** -- select a region on the timeline to narrow all logs to that window, instead of typing exact timestamps.
* **Filter by severity** -- switch out of live mode and filter to errors (or any minimum level) to focus only on what matters.
* **Filter by identifier** -- narrow logs by runner ID, request ID, or version ID, or scope to a specific endpoint.

## Investigating a Log Line

Click into any log entry for options that make debugging faster:

* **Show in context** -- when you are filtered to error-level logs, expand a single error to see the surrounding log lines and full traceback that led to it.
* **Share link** -- copy a link that opens directly to that exact log line, at that point in time and with the same filter applied, so a teammate lands on precisely what you are looking at.

## Collaborating and Exporting

Debugging production apps is often a team effort, so the Logs page makes it easy to move logs out of the dashboard:

* **Copy multiple lines** -- bulk-select log lines and copy them (as CSV) to paste into Slack for a teammate or into an agent for analysis.
* **Export as CSV** -- export the full, filtered set of logs at once.

## Related

* [Logging](../../development/logging.md) -- log scopes, what is captured, and request vs runner logs
* [Runner Analytics](runner-analytics.md) -- logs filtered to a single runner, alongside telemetry and cold start stages
* [Log Drains](log-drains.md) -- forward logs to any HTTPS endpoint in real time
