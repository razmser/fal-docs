> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../../documentation/quickstart.md) and the [Model APIs overview](../../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# fal apps runners

> List the runners of one fal application with fal apps runners. Filter by state, show runners that stopped in the last 24 hours, and read the table columns.

```bash theme={null}
Usage: fal apps runners [-h] [--team TEAM] [--env ENV]
                        [--since SINCE]
                        [--state {all,idle,running,pending,setup,crash_backoff,terminated} [...]]
                        [--output {pretty,json}] [--json]
                        app_name

List application runners.

Positional Arguments:
  app_name              Application name.

Options:
  -h, --help            show this help message and exit
  --team TEAM           The team to use.
  --env ENV             Target environment (defaults to main).
  --since SINCE         Show terminated runners since the given time. Accepts 'now', relative like '30m', '1h', '1d', or an ISO timestamp. Max 24 hours.
  --state {all,idle,running,pending,setup,crash_backoff,terminated} [{all,idle,running,pending,setup,crash_backoff,terminated} ...]
                        Filter by runner state(s). Choose one or more, or 'all' (default).

Output:
  --output {pretty,json}
                        Modify the command output
  --json                Output in JSON format (same as --output json)

Examples:
  fal apps runners my-app
  fal apps runners my-app --env staging
```

The runner table displays the following columns:

* **Alias** -- Application name
* **Machine Type** -- Hardware type (e.g. `GPU-A100`, `GPU-H100`)
* **Runner ID** -- Unique runner identifier
* **In Flight Requests** -- Number of active requests
* **Expires In** -- Time until runner expires
* **Uptime** -- How long the runner has been running
* **Revision** -- Application revision
* **State** -- Current runner state
