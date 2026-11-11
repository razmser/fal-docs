> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../documentation/quickstart.md) and the [Model APIs overview](../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Platform APIs for Serverless

> Programmatic access to serverless apps metadata, analytics, and billing

## Available Operations

The Platform APIs provide the following endpoints for Serverless Apps:

## Files

> [card] [List Root Directory Contents](../../platform-apis/v1/serverless/files/list.md)
>
> List the contents of the root directory of your Serverless storage.

> [card] [List Directory Contents](../../platform-apis/v1/serverless/files/list/directory.md)
>
> List the contents of any nested directory by providing its path.

> [card] [Download File](../../platform-apis/v1/serverless/files/file/download.md)
>
> Download any file from Serverless storage.

> [card] [Upload from URL](../../platform-apis/v1/serverless/files/file/upload-from-url.md)
>
> Upload a file from a URL into Serverless storage.

> [card] [Upload Local File](../../platform-apis/v1/serverless/files/file/upload-local.md)
>
> Upload a local file into Serverless storage.

## Requests

> [card] [List Requests by Endpoint](../../platform-apis/v1/serverless/requests/by-endpoint.md)
>
> List recent requests for your serverless endpoints with filtering, sorting, and pagination. Pass `expand=billing` to include `billable_units` per request — the per-request building block for cost reporting on your deployed apps.

## Billing

Per-request billing data for your serverless apps is available through the `expand=billing` parameter on [List Requests by Endpoint](../../platform-apis/v1/serverless/requests/by-endpoint.md). Each request in the response includes a `billable_units` field representing the units fal billed for that invocation.

> [!NOTE]
>
> `billable_units` is the raw unit count for each request. To compute total cost, multiply by your effective per-unit price for the app. For account-level credit balance, see [Account Billing](../../platform-apis/v1/account/billing.md).

```bash Example theme={null}
curl -G "https://api.fal.ai/v1/serverless/requests/by-endpoint" \
  -H "Authorization: Key $FAL_KEY" \
  --data-urlencode "endpoint_id=your-username/your-app" \
  --data-urlencode "expand=billing"
```

Sample response shape (truncated):

```json theme={null}
{
  "items": [
    {
      "request_id": "a1b2c3d4-...",
      "endpoint_id": "your-username/your-app",
      "ended_at": "2025-01-01T00:00:08Z",
      "status_code": 200,
      "duration": 7.8,
      "billable_units": 1.5
    }
  ],
  "next_cursor": "Mg==",
  "has_more": true
}
```

`billable_units` will be `null` if a billing event hasn't been recorded yet for the request (e.g., the request just completed and the billing pipeline hasn't caught up).

## Usage

> [card] [Usage](../../platform-apis/v1/serverless/usage.md)
>
> Time-bucketed, aggregated compute usage for the serverless apps **you own** — the machine-seconds your deployed apps consumed, priced with your machine rates and net of discounts. The aggregated counterpart to the per-request `billable_units` above.

Where [List Requests by Endpoint](../../platform-apis/v1/serverless/requests/by-endpoint.md) gives you per-request billing, **Usage** gives you the rolled-up view: how many machine-seconds each of your apps consumed and what it cost, grouped by app, environment, and machine type. It reports your *own* compute spend, scoped to the apps you own.

> [!NOTE]
>
> This endpoint returns billing and usage data, so it requires a key on the **`BILLING`**, **`READONLY`**, or **`FULL`** preset. A key on the `API` preset receives a `403`.

### Filtering by app

The `app` field in the response is your deployed app's name (e.g. `my-app-prod`). Two ways to narrow results:

* **`app`** — exact match on one or more app names. Comma-separated or repeated, up to 50: `app=my-app-dev,my-app-prod`. Use the value exactly as it appears in the response.
* **`search`** — case-insensitive substring match on the app name, for when you know the name but not the exact environment/version suffix: `search=my-app` returns every `my-app*` variant.

Provide both to combine them (AND). Omit both to return every app you own — useful for discovering the exact names to filter on.

```bash Summary across all your apps (last 30 days) theme={null}
curl -G "https://api.fal.ai/v1/serverless/usage" \
  -H "Authorization: Key $FAL_KEY" \
  --data-urlencode "start=2025-01-01" \
  --data-urlencode "end=2025-01-31" \
  --data-urlencode "expand=summary"
```

```bash One app, daily time series theme={null}
curl -G "https://api.fal.ai/v1/serverless/usage" \
  -H "Authorization: Key $FAL_KEY" \
  --data-urlencode "app=my-app-prod" \
  --data-urlencode "start=2025-01-01" \
  --data-urlencode "end=2025-01-31" \
  --data-urlencode "timeframe=day"
```

```bash All variants of an app by name theme={null}
curl -G "https://api.fal.ai/v1/serverless/usage" \
  -H "Authorization: Key $FAL_KEY" \
  --data-urlencode "search=my-app" \
  --data-urlencode "expand=summary"
```

Sample response shape (`expand=summary`):

```json theme={null}
{
  "summary": [
    {
      "app": "my-app-prod",
      "environment": null,
      "machine_type": "GPU-H100",
      "unit": "second",
      "quantity": 9702.47,
      "unit_price": 0.00125,
      "cost": 12.13,
      "currency": "USD",
      "is_surge": false
    }
  ],
  "next_cursor": null,
  "has_more": false
}
```

> [!NOTE]
>
> Each row is machine-seconds (`unit` is always `"second"`). Surge and non-surge usage of the same app and machine type are returned as **separate rows** (`is_surge`), so sum across them for a per-app total. Time-series `bucket` timestamps are returned in the `timezone` you request (ISO 8601 with offset, e.g. `2025-01-15T00:00:00-05:00`), which also controls how usage is grouped. `cost` is already net of your discounts.

## Logs

> [card] [Logs History](../../platform-apis/v1/serverless/logs/history.md)
>
> Query paginated logs with powerful label filters, time ranges, and search keywords.

> [card] [Logs Stream](../../platform-apis/v1/serverless/logs/stream.md)
>
> Stream live logs that match the provided filters using Server-Sent Events.

## Analytics

> [card] [Analytics](../../platform-apis/v1/serverless/analytics.md)
>
> Query time-bucketed metrics across all inbound traffic to your apps — request counts, error-type breakdown (startup, connection, timeout, runtime), latency percentiles (p25 through p99), cold boot metrics, and total billable duration. Ideal for exporting to your own observability tools.

## Metrics

> [card] [Queue Size](../../platform-apis/v1/serverless/apps/queue.md)
>
> Read the current queue backlog for your serverless applications.

> [card] [Metrics](../../platform-apis/v1/serverless/metrics.md)
>
> Export app metrics (runners, queue size, concurrent requests, throughput, and latency) in Prometheus format for custom dashboards and monitoring.
