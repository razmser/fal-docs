> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](documentation/quickstart.md) and the [Model APIs overview](documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Product Changelog

> Latest features and updates to the fal platform

What's New in Serverless

Analytics, requests, runners, logs, notifications & environments

From Alert to Root Cause

June 2026

Scaling, observability, cold starts & multi-GPU

January 2026

**October 29, 2026**

## Fallback Chains

Send a primary model and up to two fallback models in one request to `POST https://fal.run/execute`. If the primary model fails with an error that you allow, fal runs the next model. The response contains the first successful result and a record of each attempt. Each model has its own input, start timeout, and list of errors that start the next model.

[Learn more about fallback chains](documentation/model-apis/inference/fallback-chains.md)

**October 13, 2026**

## Rate Limits for Runner and App Management

fal now limits how often you can list, inspect, shell into, stop, and kill apps and runners. The limits apply to the CLI, the Python SDK, and the REST API. When you exceed a limit, the REST API returns `429 Too Many Requests`, and the CLI and SDK return the gRPC status `RESOURCE_EXHAUSTED`.

[Learn more about the limits](documentation/serverless/reliability/rate-limits.md)

**October 9, 2026**

## Key Presets and Custom Policies

Keys now use permission presets instead of the `ADMIN` and `API` scopes. When you create a key, select one of seven presets: `API`, `SERVERLESS`, `DEPLOY`, `COMPUTE`, `BILLING`, `READONLY`, or `FULL`. If no preset matches your case, select the exact permissions the key needs. You can also limit a key to specific models and set it to expire.

A preset key follows the live definition of its preset. A custom policy key, including a preset key with a model limit, keeps the permissions it had at creation. Existing `ADMIN` and `API` keys continue to work.

The Platform API reference now lists the permissions each API requires and the presets that include them.

[Learn more about key permissions](documentation/model-apis/authentication/key-based.md#permissions)

**October 9, 2026**

## Creator and Viewer Team Roles

Admins can now assign two new team roles. **Creator** can use the Playground, Sandbox, and Workflows, but cannot view request logs or manage API keys, billing, members, or roles. **Viewer** has read-only access to team resources. A Viewer can view request logs and the library, but cannot run models or change anything. Existing members keep their current roles.

[Learn more about team roles](documentation/setting-up/teams.md#roles)

**September 14, 2026**

## Billing Headers for WebSocket Endpoints

Shared WebSocket endpoints can now declare billing on their `101` upgrade response. Set `x-fal-billable-units` when the unit count is known at connect time. Set `x-fal-billable-units-webhook` to `1` when the count is only known at the end of the session. Then report the total with `POST /requests/billable-units/{request_id}`. The platform still charges sessions without either header by duration.

[Learn more about billable units](documentation/serverless/publishing-to-marketplace.md#define-billable-units)

**September 9, 2026**

## Switch Between App Endpoints in the Dashboard Playground

Deployed Serverless apps now include **Testing > Playground** in the app dashboard. Select an available endpoint, fill in its generated input form, and run a request without leaving the app. Multi-endpoint apps have one place to test their endpoints, using the existing Playground authentication and billing behavior.

[Open the guide](documentation/model-apis/playground.md#your-own-apps)

**August 17, 2026**

## Platform MCP Server

fal's second MCP server — the **Platform API MCP** — is now available at `https://api.fal.ai/v1/mcp/platform`. Where the [Run MCP](documentation/setting-up/mcp.md) lets your AI assistant *build with models*, this one lets it *operate your fal account*: connect Claude Code, Cursor, or any MCP-compatible client with your API key and ask *"why did my last request to my-app fail?"* — your assistant walks the same debugging steps you would.

**15 tools**: eleven first-class serverless debugging tools covering requests, logs, error analytics, deploy and revision history, runner and queue state, spend, and persistent storage — plus a four-tool discovery gateway that opens up the rest of the Platform API (compute, workflows, keys, account, organization, storage) without bloating your assistant's context.

```bash theme={null}
claude mcp add --transport http fal-platform \
  https://api.fal.ai/v1/mcp/platform \
  --header "Authorization: Key YOUR_FAL_KEY"
```

* **Read-only** — it can observe your account but never change it; write operations are listed in the catalog but refuse to execute
* **Stateless** — your key is sent per-request and never stored, and normal permission and ownership scoping applies
* **Free** — tool calls are the same Platform API calls you could make directly with your key

[Setup guide →](documentation/setting-up/platform-mcp.md)

**August 17, 2026**

## New Serverless Observability APIs

A batch of endpoints rounds out programmatic access to what the dashboard shows about your serverless apps:

* **[`GET /v1/serverless/apps`](https://fal.ai/docs/platform-apis/v1/serverless/apps/list)** — list your deployed apps with live state: active runners, queue size, machine types, and environment. Add `expand=endpoints` to include each app's route-level endpoint ids, in the exact form the requests and analytics APIs accept.
* **[`GET /v1/serverless/apps/{owner}/{name}/events`](https://fal.ai/docs/platform-apis/v1/serverless/apps/events)** — operational event history: deployments, configuration changes, and runner lifecycle transitions — the "what changed around this time?" API. Also surfaced in the dashboard's [events timeline](documentation/serverless/observability/app-events.md).
* **[`GET /v1/serverless/apps/{owner}/{name}/revisions`](platform-apis/v1/serverless/apps/revisions.md)** — revision history with deployment status, who deployed, and [deploy messages and annotations](documentation/deployment/deploy-to-production.md#annotating-deployments) (the API behind the August 3 entry above) — line revision boundaries up against your error timeline to spot a bad deploy.
* **[`GET /v1/serverless/apps/{owner}/{name}/runners/history`](https://fal.ai/docs/platform-apis/v1/serverless/apps/runner-history)** — historical runner counts by state (running, idle, pending, draining) for capacity analysis and incident forensics.

Together with the existing requests, logs, analytics, metrics, and usage APIs, these power the new [Platform MCP server](documentation/setting-up/platform-mcp.md) — and they're plain REST endpoints you can call directly with your API key.

**August 4, 2026**

## Narrower BILLING Key Preset

The `BILLING` key preset is now read-only for billing. A key on this preset can read usage, invoices, contracts, and billing settings. It can no longer change payment methods or billing settings. The preset now also grants read access to the list of keys and to the model catalog.

The `READONLY` preset no longer grants request sharing.

A key on a preset follows the live definition of that preset, so existing `BILLING` and `READONLY` keys picked up these changes at their next request. If the permissions of a key must stay the same for the life of the key, create the key with a custom policy. See [Key permissions](documentation/model-apis/authentication/key-based.md#permissions).

**August 4, 2026**

## GPU Utilization in Runner Telemetry

Runner telemetry now includes a dedicated **GPU Utilization** chart alongside the existing CPU, memory, and VRAM charts, so you can see how much compute your GPUs are actually doing -- not just how much memory they hold.

* **GPU Utilization** reports the average compute utilization across a runner's GPUs over time, surfaced both as a current stat and a time-series chart in the runner side sheet.
* Pair it with **VRAM Usage** to tell a memory-bound runner apart from a compute-bound one, making it easier to right-size machine types and spot under-utilized GPUs.

Open any runner from the Runners page (**Dashboard → Apps → \[your-app] → Runners**) to view its telemetry. See [Runner Analytics](documentation/serverless/observability/runner-analytics.md) for details.

**August 3, 2026**

## Deploy Messages and Annotations

You can now attach a freeform message and custom annotations to each revision when deploying:

```bash theme={null}
fal deploy my_app.py::MyApp --message "Deploying new version" --annotation GIT_SHA=a1b2c3d4
```

* Messages and annotations are set at deploy time and stay with the revision.
* View and search them on the app's **Versions** page in the dashboard, or fetch them via the [revisions API](platform-apis/v1/serverless/apps/revisions.md).
* Also available in the Python SDK: `client.deploy(..., message=..., annotations={...})`.

See [Annotating Deployments](documentation/deployment/deploy-to-production.md#annotating-deployments) for details.

**July 8, 2026**

## App-Level Retry Configuration

Apps can now register a default per-condition retry budget at deploy time with the new **`retry_config`** deployment option, using the same format as the `X-Fal-Retry-Config` request header.

* Set it as a class attribute on `fal.App` or under the app's table in `pyproject.toml`.
* Applies to all queue-based requests to the app; the app owner can still override it per-request with the `X-Fal-Retry-Config` header.
* Each condition (`server_error`, `timeout`, `connection_error`) keeps an independent budget, bounded by the platform ceiling of 10 total attempts.

See [Retries and Error Handling](documentation/serverless/reliability/retries.md#app-level-retry_config) for details.

**June 17, 2026**

## New Serverless Usage Page

The new **[Serverless Usage page](https://fal.ai/dashboard/serverless-usage)** (Serverless → Usage) provides attributable, dimensioned reporting of serverless compute spend in the dashboard.

* **Compute reported in machine-seconds**, broken down per app, environment, and machine type.
* **Spend is split by price category: Reserve, Burst, On-Demand, and List.** This distinguishes consumption covered by committed reserved capacity from burst overage and undiscounted list-rate usage, supporting reservation sizing and workload placement decisions.
* **Group by app or by machine type**, including a dedicated **Usage by machine type** view, to identify the deployments and hardware classes driving cost.
* Billing-cycle and custom time-range presets are supported. On-demand quotes are reported as a distinct price category, and organization-level reporting now spans both serverless and compute.

This is the same dataset returned by the [`GET /v1/serverless/usage`](platform-apis/v1/serverless/usage.md) API, surfaced in the dashboard.

**June 16, 2026**

## Redesigned Usage & Billing Page

The **[Usage & Billing page](https://fal.ai/dashboard/usage-billing)** has been rebuilt to support usage attribution (the Settings "Overview" tab is now "Usage").

* **Filter by auth method, API key, and login username** to attribute consumption to a specific credential or user.
* Normalized app names and revised charts improve readability and export.

**June 16, 2026**

## Deploy Existing Docker Servers Without Code Changes

Existing Docker-based servers (ComfyUI, custom APIs) can now be deployed without rewriting them as a `fal.App`, reducing migration effort from self-hosted infrastructure, Kubernetes, or RunPod.

* **Direct Server Mode:** set `exposed_port` in `pyproject.toml` and fal routes traffic directly to the container port. Existing routes, middleware, and response formats are preserved.
* **Prebuilt image references (no Dockerfile required):** reference an image already pushed to a registry (`image = "my-org/my-server:latest"`) and override `entrypoint`/`cmd` under `[tool.fal.apps.<name>.image]`.
* **Private registry support:** pull from Docker Hub, Google Artifact Registry, or Amazon ECR by supplying registry credentials in the image configuration.
* Migration guides are available for [external Docker servers](documentation/development/migrate-external-docker-server.md) and [RunPod](documentation/development/migrate-from-runpod.md), along with the [`pyproject.toml` reference](api-reference/python-sdk/pyproject-toml.md) and [Private Docker Registries](documentation/development/private-registries.md).

**June 15, 2026**

## Serverless Usage API

* New **[`GET /v1/serverless/usage`](platform-apis/v1/serverless/usage.md)** endpoint reports your own serverless compute usage — the machine-seconds each of your deployed apps consumed, priced with your machine rates and net of discounts
* Break usage down per app, environment, and machine type, with `time_series` and `summary` views over any date range and timeframe
* Filter by app name with `app` (exact, one or more) or `search` (case-insensitive substring) — match a deployed app by name without needing its exact version suffix
* Ideal for per-app cost reporting and exporting to your own billing/observability tools
* Complements the per-request `billable_units` on [`GET /v1/serverless/requests/by-endpoint`](platform-apis/v1/serverless/requests/by-endpoint.md) with an aggregated, time-bucketed view

**June 7, 2026**

## New Capacity Page

The new **[Capacity page](https://fal.ai/dashboard/capacity)** (Serverless → Capacity) reports GPU consumption against your account limit, providing visibility into available headroom before requests begin queuing.

* **Account-wide GPU usage over time, plotted against your limit**, with baseline and burst reference lines indicating utilization relative to reserved capacity.
* **Per-GPU-type breakdown** of capacity consumption by hardware class.
* **Approaching-limit and at-limit indicators** on runner and app views, with GPU counts shown on runners.
* **Request more capacity** action for requesting a higher limit.

**June 5, 2026**

## Per-Condition Retry Configuration

The new **`X-Fal-Retry-Config`** request header replaces all-or-nothing retry behavior with a separate retry budget per failure condition, allowing retry policy to be tuned independently for each failure mode.

* Map each retry condition (`server_error`, `timeout`, `connection_error`) to its own `retries` count (for example, retry transient server errors while disabling retries on timeouts).
* Each condition maintains an independent budget, bounded by the platform ceiling of 10 total attempts.
* Applies to apps you own and takes precedence over status-code defaults. `x-fal-no-retry` takes precedence when both are set.
* Complements app-level `skip_retry_conditions` and the per-response `X-Fal-Needs-Retry` and `x-fal-stop-runner` controls.

See [Retries and Error Handling](documentation/serverless/reliability/retries.md) for the full reference.

**June 2, 2026**

## Filter and Sort Apps

Managing a large number of serverless apps is now easier with new filtering and sorting controls directly on the [apps page](https://fal.ai/dashboard/apps).

* **Filter by machine type** to instantly isolate apps running on specific hardware (e.g., H100, A100, L40) and monitor targeted resource allocation.
* **Sort by active runners, GPUs, or queue size** to instantly surface the apps consuming the most capacity or backing up with queued requests.

![New filter by machine type and sorting options for runners, GPUs, and queue size](https://mintcdn.com/fal-d8505a2e/ND7LYAPTZuIL9ZIB/images/changelog/apps-filters.png?fit=max\&auto=format\&n=ND7LYAPTZuIL9ZIB\&q=85\&s=d5db77ed01bd9fc1d93279d0bb95752d)

Works in both card view and list view, and stacks with environment and tag filtering so you can find what needs your attention faster.

**June 1, 2026**

## Expanded Analytics Metrics

The [`GET /v1/models/analytics`](platform-apis/v1/models/analytics.md) and [`GET /v1/serverless/analytics`](platform-apis/v1/serverless/analytics.md) endpoints now support fourteen additional metrics via the `expand` parameter, matching the metric families the dashboard surfaces:

* **Error type breakdown** — `startup_error_count`, `connection_error_count`, `timeout_error_count`, `runtime_error_count` for finer-grained failure attribution beyond the existing 4xx/5xx counts
* **Cold boot metrics** — `cold_boot_count`, `p50_cold_boot_duration`, `p75_cold_boot_duration`, `p90_cold_boot_duration` for tracking startup latency
* **Extended latency percentiles** — `p25_duration`, `p95_duration`, `p99_duration`, `p95_prepare_duration`, `p99_prepare_duration` for tail-latency analysis
* **Total billable duration** — `total_billable_duration` for aggregate billable execution time (the sum of request execution durations)

Existing metrics and response shape are unchanged; only new optional fields are added.

**May 29, 2026**

## Configurable Termination Grace Period

The runner termination grace period is now configurable, allowing long-running requests to drain and `teardown()` to complete before `SIGKILL`, which reduces the risk of dropped work or inconsistent state during scale-down.

* **`termination_grace_period_seconds` is configurable up to 1 hour** (previously capped at 30 seconds, default 5 seconds).
* The grace period is a shared budget for completing in-flight requests and running `teardown()`. Use `handle_exit()` to signal long requests to stop early so cleanup is not skipped.
* `keep_alive` now applies to both idle-after-request and never-served runners.

See [Scale Your Application](documentation/deployment/scale-your-application.md#termination_grace_period_seconds) for details.

**April 29, 2026**

## Aggregate Analytics Components

![Serverless apps page aggregate analytics dashboard with GPU and CPU distribution, live capacity tiles, traffic and concurrency charts, top apps, and a per-app stacked requests breakdown](https://mintcdn.com/fal-d8505a2e/NH1xMgqfuYMzBqdq/images/changelog/apps.png?fit=max\&auto=format\&n=NH1xMgqfuYMzBqdq\&q=85\&s=39b65eb1b8d4b4f4d0376c0a11b57a05)

The Serverless apps page now opens with an aggregate analytics dashboard that summarizes capacity, traffic, and per-app activity across every app you own in one place.

* **GPU and CPU distribution** charts visualize how your active runners are split across machine types (H100, A100, L40, B200, CPU types, etc.)
* **Live capacity tiles** show total **Active Runners, Queued Requests, GPUs, and CPUs**, each annotated with how many apps contribute to the number
* **Status code, request traffic, and concurrent request** charts plot aggregate request health over your selected window
* **Top Apps** lists your highest-volume apps over the window for quick drill-in
* **Requests by app** is a new stacked chart that breaks aggregate traffic down per app, with a searchable legend you can click to isolate a single app or flip to an **Errors** view

**April 28, 2026**

## Legacy CDN Cleanup — June 1st, 2026

On **June 1st, 2026**, any legacy CDN object not accessed in the past 6 months will be deleted.

**How to identify legacy objects:** Legacy URLs look like `/files/[animal]/[filename]` (e.g. `/files/otter/AFsfdoliuhhvf.mp4`). Anything served from `/files/b/...` is on our current CDN and is unaffected.

**If you've accessed it recently, it's already safe:** Any legacy object accessed in the last 6 months has already been automatically migrated to our current CDN — no action needed on your end. The URL is unchanged and the object will be preserved.

**To keep a legacy object you haven't touched in a while:** Access it once before **June 1st, 2026**. That single access triggers the automatic migration and preserves the object. The URL stays the same.

**What stays the same:**

* Legacy URLs continue to work — no code changes needed
* Anything new you create already uses the modern CDN (`/files/b/...`)

**April 27, 2026**

## Deploy ComfyUI on fal — Visual Control

Run ComfyUI workflows on fal serverless GPUs without writing a single line of code. Two pre-built zip packages, one double-click — your team is generating on H100s in minutes.

* **User package** — a tiny launcher UI on `localhost:9998`. Paste your fal API key and the endpoint slug your team lead gave you, click **Start**, and the full ComfyUI canvas opens on `localhost:8188` running against the cloud GPU.
* **Admin package** — a complete management UI on `localhost:9999`. Add models, custom nodes, and Python deps from a table, set HuggingFace / Civitai tokens for gated weights (auto-pushed to fal secrets at deploy), and ship with one click. Live runner observability, an outputs gallery, and an undeploy switch are built in.

![Admin web UI showing the Models tab — folders, filenames, download URLs in a table](https://mintcdn.com/fal-d8505a2e/Un5AMpXNKs4_TsjB/images/changelog/comfyui-visual-admin.png?fit=max\&auto=format\&n=Un5AMpXNKs4_TsjB\&q=85\&s=f0f6c87a56d04a741a6a535ea505cc84)

The launcher auto-installs `uv` and Python 3.11 on first run — no Python knowledge required from end users. Works on macOS and Windows.

[Read the guide →](examples/image-generation/run-comfyui-visual.md)

**April 23, 2026**

## Runner Termination Reason

Diagnose why a runner was terminated directly from the dashboard. The reason now appears across every runner surface.

* **Runner detail page** — termination reason is shown inline next to the state.

![Runner detail page state section showing termination reason badge](https://mintcdn.com/fal-d8505a2e/GdCSIoK3y-dVum8C/images/changelog/runner-terminate-reason-detail.png?fit=max\&auto=format\&n=GdCSIoK3y-dVum8C\&q=85\&s=b855acfc34426bf63bdcc0877d1a2bb1)

* **Runner list** — surfaces the same reason via a tooltip on the state badge, so you can scan failures without opening each runner

![Runner list with termination reason tooltip on the state badge](https://mintcdn.com/fal-d8505a2e/GdCSIoK3y-dVum8C/images/changelog/runner-terminate-reason-list.png?fit=max\&auto=format\&n=GdCSIoK3y-dVum8C\&q=85\&s=86dcc994543b9f7ad36e7ccd4d9a3d9a)

* **Runner events timeline** — event rows display the termination reason as a highlighted badge alongside the status text

![Runner event row showing termination reason as a highlighted badge alongside the status text](https://mintcdn.com/fal-d8505a2e/GdCSIoK3y-dVum8C/images/changelog/runner-terminate-reason-events.png?fit=max\&auto=format\&n=GdCSIoK3y-dVum8C\&q=85\&s=d27c350bee87a53a7357c64e52a3dcd9)

Please note that in some cases the termination reason may be unknown. When that happens, no reason badge or tooltip is shown.

**April 23, 2026**

## fal Workflow

* Improved the model search experience
* Improved API documentation
* Smoother workflow when adding models
* Node IDs are visible in the UI
* Nodes can be locked
* Media-type icons on node handles
* Performance improvements and bug fixes

**April 14, 2026**

## Model API Reference

We've launched a comprehensive **[Model API Reference](model-api-reference.md)** — auto-generated documentation for every top model on fal.ai, covering video generation, image generation, audio, vision, and 3D.

Each model page includes:

* **Full input/output schemas** with parameter descriptions, types, and defaults
* **Quick Start code examples** in Python, JavaScript, and cURL
* **Real prompt-and-output examples** pulled directly from the fal.ai playground
* **Rich model descriptions** with feature breakdowns, comparisons, and technical specs
* **Direct playground links** to try models interactively

The reference is organized by model family (FLUX, Kling Video, Seedance, Nano Banana, Veo, and more) with a navigation structure that mirrors the endpoint hierarchy. Browse by category or jump straight to any endpoint.

[Explore the Model API Reference →](model-api-reference.md)

**April 10, 2026**

## Serverless Analytics API

* New **[`GET /v1/serverless/analytics`](platform-apis/v1/serverless/analytics.md)** endpoint lets serverless app owners query time-bucketed analytics across all inbound traffic to their endpoints
* Retrieve request counts, success/error rates, and latency percentiles (p50/p75/p90) for any date range and timeframe
* Ideal for exporting analytics to your own tools like BigQuery, Grafana, or Datadog
* Requires endpoint ownership — only the app owner can access these metrics
* The existing [`GET /v1/models/analytics`](platform-apis/v1/models/analytics.md) continues to show your own request activity as a caller

**April 1, 2026**

## Latency Percentile Chart

![Latency percentile chart showing p50, p90, p95, and p99 over time](https://mintcdn.com/fal-d8505a2e/_K0ozUKUZUyNXY0L/images/changelog/latency-percentile-chart.png?fit=max\&auto=format\&n=_K0ozUKUZUyNXY0L\&q=85\&s=b652d30a1d10d515e5c2adada4b7c364)

* The [analytics page](documentation/serverless/observability/app-analytics.md) now includes a **latency percentile chart** showing p50, p90, p95, and p99 request latency over time
* Quickly spot latency regressions or tail-latency spikes at a glance

**March 31, 2026**

## Multi-Endpoint Aggregation in Analytics

![Analytics endpoint selector with All option for aggregated metrics](https://mintcdn.com/fal-d8505a2e/_K0ozUKUZUyNXY0L/images/changelog/multi-endpoint-aggregation.png?fit=max\&auto=format\&n=_K0ozUKUZUyNXY0L\&q=85\&s=612727e3c34f436fb147b05517d4bdd4)

* Select **"All" endpoints** in [App Analytics](documentation/serverless/observability/app-analytics.md) to see metrics aggregated across every endpoint at once
* Aggregation works across request counts, error rates, percentile charts, and gateway stats, so you can get a full picture of your app's performance without switching between endpoints

**March 31, 2026**

## GPU and Runner Metrics on Apps Page

![Apps page with aggregate metrics bar showing runners, queue depth, and GPU distribution](https://mintcdn.com/fal-d8505a2e/_K0ozUKUZUyNXY0L/images/changelog/gpu-runner-metrics-apps.png?fit=max\&auto=format\&n=_K0ozUKUZUyNXY0L\&q=85\&s=4d30827b73e8c4dab8707307896f688d)

* The [apps page](documentation/deployment/manage-deployments.md) now shows an **aggregate metrics bar** with total runners, queue depth, and GPU distribution, giving you a quick overview of your resource usage without clicking into each app
* **Per-app GPU and queue columns** in both card view and list view make it easy to compare usage across apps at a glance

**March 31, 2026**

## Logs Endpoint Filtering and Source Column

![Logs view with endpoint filter and source column](https://mintcdn.com/fal-d8505a2e/_K0ozUKUZUyNXY0L/images/changelog/logs-endpoint-source.png?fit=max\&auto=format\&n=_K0ozUKUZUyNXY0L\&q=85\&s=0637fb4efb04c9488924b69b2a91cff1)

* **Filter [logs](https://fal.ai/docs/serverless/development/logging.md) by endpoint** to focus on a specific route
* A new **Source column** shows which endpoint or system action produced each log line

**March 30, 2026**

## Notifications Overhaul

![Redesigned notifications dropdown with pagination and mark all read](https://mintcdn.com/fal-d8505a2e/_K0ozUKUZUyNXY0L/images/changelog/notifications-overhaul.png?fit=max\&auto=format\&n=_K0ozUKUZUyNXY0L\&q=85\&s=ead345f1f0c40abef26501511f790595)

* **Redesigned notification dropdown** so you can stay on top of issues without leaving the page you're on
* **Category filtering** lets you cut through noise and focus on the notification types that matter to you
* **Pagination** so you can browse your full notification history without losing your place
* **Mark all as read** to quickly clear your inbox when you've caught up
* **OOM alerts** notify you when a runner runs out of memory, so you can resize before it impacts users
* **Notification badge on app tabs** shows unread counts per app so you know exactly which apps need attention

See [notification settings](documentation/serverless/observability/slack-notifications.md) to configure alerts.

**March 28, 2026**

## Cold Start and Queue Percentiles

![Analytics page showing cold start ratio, cold start percentiles, and queue wait percentiles](https://mintcdn.com/fal-d8505a2e/_K0ozUKUZUyNXY0L/images/changelog/cold-start-queue-percentiles.png?fit=max\&auto=format\&n=_K0ozUKUZUyNXY0L\&q=85\&s=17f96717c19e1fa0ac28027d8a1d49d5)

* **Cold start ratio** shows what percentage of requests hit a cold start, so you can measure the impact of your scaling configuration
* **Cold start duration percentiles** (p50, p75, p90, p95) help you understand whether cold starts are a minor delay or a real bottleneck for your users
* **Queue wait percentiles** (p50, p75, p90, p95) reveal how long requests wait before a runner picks them up, helping you decide if you need more concurrency
* A new **startup breakdown card** shows exactly where startup time goes (image pull, setup, etc.) so you know what to optimize
* The [analytics](documentation/serverless/observability/app-analytics.md) layout has been redesigned with expandable sections so you can focus on the metrics that matter most

**March 28, 2026**

## Request Throughput Charts

![Charts showing processed requests per second, received requests per second, and concurrent requests](https://mintcdn.com/fal-d8505a2e/_K0ozUKUZUyNXY0L/images/changelog/request-throughput-charts.png?fit=max\&auto=format\&n=_K0ozUKUZUyNXY0L\&q=85\&s=63a293ed8a4ebec930f7f3a42eff8be3)

New charts are available on the [App Analytics](documentation/serverless/observability/app-analytics.md) Requests tab:

* **Processed requests per second** chart shows your inference throughput over time
* **Received requests per second** chart shows the inbound request rate
* **Concurrent requests** chart shows how many requests are in-flight simultaneously

**March 28, 2026**

## API Key Tags and Editing

![Keys page with tags column and editable key descriptions](https://mintcdn.com/fal-d8505a2e/_K0ozUKUZUyNXY0L/images/changelog/api-key-tags-editing.png?fit=max\&auto=format\&n=_K0ozUKUZUyNXY0L\&q=85\&s=433861fbc2313a08b37a7b53c64a55d7)

* **Tags on [API keys](documentation/setting-up/authentication.md)** let you organize keys with the same tagging system used for apps
* **Editable key descriptions** can now be updated directly from the dashboard without recreating the key

**March 27, 2026**

## Request Timing Breakdown

![Request list showing duration broken into startup time and execution time](https://mintcdn.com/fal-d8505a2e/_K0ozUKUZUyNXY0L/images/changelog/request-timing-breakdown.png?fit=max\&auto=format\&n=_K0ozUKUZUyNXY0L\&q=85\&s=905043093a77cbf45aa840451a649378)

* Request duration in the [requests list](https://fal.ai/docs/serverless/deployment-operations/monitor-performance.md) now shows a **breakdown into startup time and execution time**
* Hover over the duration to see the full timing detail

**March 27, 2026**

## Environment Manager

![Environment manager dialog for creating and deleting environments](https://mintcdn.com/fal-d8505a2e/_K0ozUKUZUyNXY0L/images/changelog/environment-manager.png?fit=max\&auto=format\&n=_K0ozUKUZUyNXY0L\&q=85\&s=f2ddcafc9f772e06c0aff90ec68643aa)

* A new **[environment manager](https://fal.ai/docs/serverless/deployment-operations/manage-environments.md) dialog** lets you create and delete environments in one place
* The environment selector is now **searchable**, replacing the old dropdown

**March 25, 2026**

## Requests Page Overhaul

![Requests page with status code charts, p90 latency, and sortable request list](https://mintcdn.com/fal-d8505a2e/o8eW4dsVeOWMw5el/images/changelog/requests-page-overhaul.png?fit=max\&auto=format\&n=o8eW4dsVeOWMw5el\&q=85\&s=6f0dec1c687f0a72c19529ae9502d6bc)

* **Status code and latency charts** — the requests page now shows interactive charts for request status code distribution and p90 latency over time, giving you an instant picture of your app's health
* **Click and drag to filter** — select a time range on any chart to filter the request list below to just that window, making it easy to isolate a spike or incident
* **Sort by cold start, execution time, and latency** — sort the request list by longest cold start duration, execution time, total latency, and more to quickly surface the slowest or most problematic requests
* **Jump to Analytics** — a direct link takes you from the requests page to App Analytics with your current filters pre-applied

**March 23, 2026**

## Runner Summary on App Overview

![Runner summary card showing active runner counts, state breakdowns, and concurrency trend](https://mintcdn.com/fal-d8505a2e/o8eW4dsVeOWMw5el/images/changelog/runner-summary-card.png?fit=max\&auto=format\&n=o8eW4dsVeOWMw5el\&q=85\&s=659748d93f5c17e43f616c0c86d99973)

* The app overview page now includes a **runner summary card** showing active runner counts, state breakdowns, and a concurrency trend chart at a glance
* Quickly see how many runners are in each state (running, idle, pending, setup) without navigating to the Runners page

**March 20, 2026**

## Search Runners by ID

![Runner ID search on the Runners page](https://mintcdn.com/fal-d8505a2e/o8eW4dsVeOWMw5el/images/changelog/runner-id-search.png?fit=max\&auto=format\&n=o8eW4dsVeOWMw5el\&q=85\&s=f1b14ce93d0862ff6364bcd8e5806cb7)

* Search for a specific runner by its ID on the Runners page — useful when you have a runner ID from logs, alerts, or incident reports
* Results display instantly with full runner details, and the Refresh button re-fetches the searched runner

**March 20, 2026**

## Error Types in App Requests and Errors pages

![Error type badges on request logs showing specific failure reasons](https://mintcdn.com/fal-d8505a2e/o8eW4dsVeOWMw5el/images/changelog/error-types-ui.png?fit=max\&auto=format\&n=o8eW4dsVeOWMw5el\&q=85\&s=28a9aeb6cfea6071d12d5858af5c9ae7)

* Failed requests in the dashboard now show the **specific reason** for the failure — not just a status code, but whether it was a `request_timeout`, `runner_disconnected`, `startup_timeout`, `runner_scheduling_failure`, and more
* Instantly understand if a 500 was caused by your code crashing, the runner running out of memory, or a scheduling issue — without digging through logs
* Error types are shown as badges on request rows and in the request detail view, matching the `error_type` field in [API responses](https://fal.ai/docs/model-apis/errors.md#request-error-types)

**March 20, 2026**

## Granularity Picker on App Overview Chart

![Granularity picker on the app overview chart](https://mintcdn.com/fal-d8505a2e/o8eW4dsVeOWMw5el/images/changelog/granularity-picker.png?fit=max\&auto=format\&n=o8eW4dsVeOWMw5el\&q=85\&s=4caf1dec51215ceb44a02a7d5bfd48cb)

* The app overview chart now includes a **granularity picker** to control the time bucket size — defaults to 10-minute intervals
* Choose finer or coarser intervals to zoom into specific traffic patterns or view broader trends

**March 16, 2026**

## Enhanced App Cards

* App cards on the listing page now show **separate client and server error rates** for clearer error attribution
* Updated **machine type icons** for better visual identification of GPU types
* Improved card layout with more readable stat values

**March 20, 2026**

## REST API `/metrics` Improvements

Runner-level resource metrics are now available through the REST API:

* **Runner resource metrics** — CPU, memory, GPU, and VRAM usage are now included in `/metrics`, summed across all runners per app
* **Per-runner metrics endpoint** — `GET /metrics/runners/{runner_id}` returns resource metrics for an individual runner

**March 18, 2026**

## fal MCP Server

The fal MCP server is now available at `mcp.fal.ai`. Connect any AI assistant that supports the [Model Context Protocol](https://modelcontextprotocol.io) — Claude Code, Claude Desktop, Cursor, Windsurf, and more — to the full fal platform.

**9 tools** let your assistant search 1,000+ models, check schemas and pricing, run inference, upload files, and browse documentation — all from a single conversation.

```bash theme={null}
claude mcp add --transport http --scope user fal https://mcp.fal.ai/mcp-relay
```

Then run `/mcp` in Claude Code, select `fal`, and sign in through your browser. No API key or authorization header is needed.

The server is fully stateless and free to use. You only pay for the model runs you trigger.

* [Setup guide](documentation/setting-up/mcp.md)

**March 17, 2026**

## `x-fal-stop-runner` Response Header

You can now control whether a runner is terminated after a response using the `x-fal-stop-runner` response header, independent of the HTTP status code.

* Set `x-fal-stop-runner: true` to force runner termination with any status code (same effect as a 503 on the runner, without requiring a 503 response)
* Set `x-fal-stop-runner: false` to keep the runner alive even when returning a 503 (useful when you want the retry behavior of a 503 without killing the runner)
* The header is stripped from the response before it reaches the caller
* Combine with `X-Fal-Needs-Retry` to fully decouple retry behavior from runner lifecycle — for example, retry on a fresh runner (`X-Fal-Needs-Retry: 1` + `x-fal-stop-runner: true`) or retry while keeping the current runner alive (`X-Fal-Needs-Retry: 1` + `x-fal-stop-runner: false`)

See [Retries and Error Handling](documentation/serverless/reliability/retries.md#per-response-x-fal-stop-runner) for full details.

**March 12, 2026**

## Delete App Versions

* You can now **delete individual app versions** directly from the dashboard
* Useful for cleaning up old or failed deployments without needing the CLI
* A confirmation dialog prevents accidental deletions

**March 12, 2026**

## Shareable Runner Details

* Runner slideout panels are now **shareable via URL** — click on a runner and copy the link to share the exact runner view with a teammate
* Helpful for debugging runner issues collaboratively or linking from incident reports

**March 12, 2026**

## Endpoint Filter in Request Logs

* Filter your request logs by **specific endpoint** to focus on traffic to a particular route (e.g. `/predict` vs `/health`)
* Works alongside existing filters for status codes, date ranges, and apps

**March 11, 2026**

## Interval Selection and Chart Zoom in Analytics

* The analytics pages now include an **Interval** picker that lets you control how metrics are bucketed — choose from **30 seconds**, **1 minute**, **5 minutes**, **10 minutes**, **1 hour**, **1 day**, **1 week**, or **1 month** depending on your selected date range
* Use finer intervals (30 sec, 1 min) to pinpoint exact moments when latency spiked or error rates changed, and wider intervals (1 day, 1 week) for high-level trends over longer periods
* **Click and drag on any chart** in [App Analytics](documentation/serverless/observability/app-analytics.md) to select a time range and instantly zoom into that window — the date range updates automatically so you can isolate an incident, compare before-and-after a deployment, or drill into a traffic spike without manually adjusting date pickers
* The Interval picker is available on both the **Requests** and **Runners** tabs in App Analytics, as well as in [Error Analytics](documentation/serverless/observability/error-analytics.md)

**March 11, 2026**

## App List View

* Toggle between **card view** and a new compact **list view** on the app listing page using the view switcher in the toolbar
* List view displays your apps as rows with columns for **type**, **runners**, **requests/min**, **latency**, **errors**, **machine type**, and **last updated** — making it easy to scan and compare performance across many apps at once
* Your view preference is saved automatically, so you'll see the same layout next time you visit
* Especially useful for teams managing dozens of serverless apps who need to quickly spot which apps need attention

**March 11, 2026**

## Error Types in API Responses

* Error responses now include a machine-readable **`error_type`** field identifying the specific failure category — such as `request_timeout`, `runner_disconnected`, `runner_scheduling_failure`, or `runner_server_error`
* Use `error_type` to build smarter retry logic: runner and timeout errors (e.g. `runner_connection_timeout`, `startup_timeout`) are typically transient and worth retrying, while client errors like `bad_request` should not be
* Track `error_type` values in your monitoring to spot trends — for example, frequent `runner_scheduling_failure` errors may indicate you need to increase `max_concurrency`

See [Error Reference — Request Error Types](https://fal.ai/docs/model-apis/errors.md#request-error-types) for the full list of error types, status codes, and handling guidance.

**March 11, 2026**

## Termination Grace Period

* New `termination_grace_period_seconds` parameter lets you control how long a runner has to finish in-flight requests and run `teardown()` before being forcefully killed
* Defaults to **5 seconds**, configurable up to a maximum of **30 seconds**

```python theme={null}
class MyApp(fal.App):
    termination_grace_period_seconds = 20
```

See [Scale Your Application — Termination Grace Period](https://fal.ai/docs/serverless/deployment-operations/scale-your-application.md#termination-grace-period) for the full shutdown lifecycle and best practices.

**February 25, 2026**

## App Tagging

* You can now create and assign **custom tags** to your serverless apps directly from the dashboard
* Organize your app listing by tagging apps by team, project, model type, or any label that makes sense for your workflow
* **Filter by tag** in the app list to quickly find the apps you need
* Create, assign, and remove tags right from the app listing page — no CLI or API needed

Tags are visual labels for organizing your dashboard — they don't affect app behavior, routing, or secrets. For isolated deployment stages (dev, staging, production), use [Environments](https://fal.ai/docs/serverless/deployment-operations/manage-environments.md) instead.

**February 23, 2026**

## Machine Type in Runner Output

* `fal runners list` and `fal app runners <app>` now display the **machine type** for each runner (e.g. `GPU-A100`, `GPU-H100`)
* JSON output (`--output json`) also includes the `machine_type` field for each runner

**February 17, 2026**

## Runner `CRASH_BACKOFF` Status

* Runners that fail during `setup()` now show a `CRASH_BACKOFF` status, making it easier to identify runners that are in a cooldown period before retrying initialization
* You can filter runners by this state using `fal runners list --state crash_backoff`
* See [Understanding Runners](https://fal.ai/docs/serverless/deployment-operations/understand-runners.md#runner-states) for details

**February 16, 2026**

## Interactive Log Histogram

* The logs page now features an interactive histogram that visualizes log volume over time, broken down by severity level
* **Click and drag** to select a time range on the histogram to zoom into that window and filter your logs instantly
* **Zoom in and out** to explore log patterns at different time granularities
* Color-coded bars show the distribution of stderr, error, warning, info, and trace logs at a glance

## Jump to Context

* When viewing a specific log entry, use the new **Jump to Context** button to instantly scroll to surrounding log lines
* Quickly see what happened before and after any log entry without manually searching through timestamps
* Especially useful when navigating to a log from a shared link or alert

## Switch Log Timezones Between UTC and Local

* You can now toggle between **UTC** and your **local timezone** directly from the datepicker in the logs page
* All log timestamps, filters, and the histogram update instantly when switching timezones

**February 12, 2026**

## Serverless App Cards with Error Rates and Graphs

* The app listing page now displays rich cards for each serverless app with at-a-glance performance metrics
* **Error rate indicators** show the health of each app directly on the card
* **Inline sparkline graphs** visualize request volume and error trends over time
* Quickly identify apps that need attention without clicking into each one individually

**February 11, 2026**

## Share Logs with Your Team

* You can now click on any log entry and share the link directly with the rest of your team
* Shared log links preserve the full context of the log, making it easy to collaborate on debugging and troubleshooting

## Runner Side Sheet

* Click into any runner on the Runners page to open a detailed side sheet with telemetry, logs, and the ability to connect to the runner
* Makes it much easier to debug and observe your runners without leaving the page

**February 11, 2026**

## `--auth` flag for `fal run`

You can now specify the authentication mode when running your app with `fal run` using the `--auth` flag. Supported values are `public` and `private`, giving you control over who can access your app during development and testing.

```bash theme={null}
fal run path/to/myapp.py::MyApp --auth private
```

* `public` — no authentication required, app owner pays
* `private` — only you or your team can access

**February 4, 2026**

## Full-Screen Logs

* The logs page now opens in a **full-screen view**, giving you significantly more vertical and horizontal space to work with
* See more log lines at once and reduce scrolling when debugging complex issues

**February 3, 2026**

## FalBaseModel for better input/output definitions

Define your API inputs and outputs with `FalBaseModel` a Pydantic base class with built-in support for hidden fields, field ordering, and media type hints.

* **Hidden fields** - Use `Hidden(Field(...))` to mark parameters as API-only, hiding them from the playground UI while keeping them accessible via API
* **Field ordering** - Control the order of fields in your API schema with `FIELD_ORDERS`
* **Media field helpers** - Use `ImageField`, `AudioField`, `VideoField`, and `FileField` for better playground rendering

Example:

```python theme={null}
from fal.toolkit import FalBaseModel, Field, Hidden, ImageField

class TextToImageInput(FalBaseModel):
    FIELD_ORDERS = ["prompt", "negative_prompt", "image_size"]

    prompt: str = Field(description="Text description of the image")
    negative_prompt: str = Field(default="", description="What to avoid")
    image_size: str = Field(default="1024x1024")

    # Hidden from playground but accessible via API
    debug_mode: bool = Hidden(Field(default=False))
    internal_seed: int = Hidden(Field(default=-1))

class ImageToImageInput(FalBaseModel):
    image_url: str = ImageField(description="Input image")
    strength: float = Field(default=0.8)
```

See [Handle Inputs and Outputs](https://fal.ai/docs/serverless/development/handle-inputs-and-outputs.md#falbasemodel) for details.

**February 2, 2026**

## Switch environments from app pages

You can now quickly switch between environments directly from any app page using the new environment dropdown in the dashboard.

* **Environment dropdown** - Click the environment badge on any app page to see all environments where the app is deployed
* **One-click switching** - Select an environment to navigate to the same app in that environment
* **Quick access** - View environment secrets or create new environments directly from the dropdown

**January 30, 2026**

## Track when runners are idle and waiting for work

> [!WARNING]
>
> **Potential Breaking Change** - The runner state model has been updated. If
> you're monitoring or tracking runner states programmatically, you may need to
> update your integration to handle the new IDLE state.

Understand runner utilization with the new IDLE state that shows when runners are ready but not actively processing requests.

* **IDLE state visibility** - see when runners finish processing and are waiting for new work
* **Better resource monitoring** - distinguish between actively processing requests and waiting states
* **Improved observability** - track idle time to optimize scaling and resource allocation

See the new [Understanding Runners](https://fal.ai/docs/serverless/deployment-operations/understand-runners.md) guide and [Optimizing Cold Starts](https://fal.ai/docs/serverless/deployment-operations/optimize-cold-starts.md) guide for complete details on runner lifecycle, states, and performance optimization.

**January 25, 2026**

## Control queue wait time with `start_timeout`

You can now set a `start_timeout` on requests which ensures that when queue time is too long, the request is aborted without starting.

See [Client Libraries → Start Timeout](https://fal.ai/docs/model-apis/client.md#2-queue-management) for details.

**January 21, 2026**

## Environments for isolated deployments

Organize your applications, secrets, and configurations across different stages of your workflow with the new environments feature.

* **Create isolated environments** for development, staging, and production
* **Environment-scoped secrets** - use different API keys and credentials per environment
* **Deploy to specific environments** using the `--env` flag

```bash theme={null}
# Create a staging environment
fal environments create staging

# Deploy to staging
fal deploy my_app.py --env staging

# Set environment-specific secrets
fal secrets set API_KEY=staging-key --env staging
```

See [environments documentation](https://fal.ai/docs/serverless/deployment-operations/manage-environments.md) for details.

**January 19, 2026**

## Handle graceful shutdown with `handle_exit()` and `teardown()`

You can now define `handle_exit()` and `teardown()` methods in your app to handle graceful shutdown.

* **`handle_exit()`** - Called when the runner is requested to terminate to signal handlers to stop early.
* **`teardown()`** - Called when the runner is shutting down to clean up resources.

See [lifespan docs](https://docs.fal.ai/serverless/getting-started/core-concepts#lifespan) for details.

**January 13, 2026**

## Include local files in container builds with COPY and ADD

> [!WARNING]
>
> **Experimental Feature** - This feature is currently experimental.

You can now use standard Docker `COPY` and `ADD` commands to include local files in your container builds.

* **Automatic file parsing** - fal parses your Dockerfile to find COPY/ADD commands and collects referenced files
* **Hash-based deduplication** - Files are uploaded to fal's storage with content-addressable deduplication (files from `app_files` are reused automatically)
* **.dockerignore support** - Create a `.dockerignore` file or use `add_dockerignore()` to exclude files
* **Multi-stage build support** - `COPY --from=...` commands are correctly handled (only local files are collected)
* **Smart rebuilds** - Changes to your `fal.App` file don't trigger rebuilds (it's pickled separately); only changes to COPY/ADD referenced files trigger rebuilds

Example:

```python theme={null}
dockerfile_str = """
FROM python:3.11
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY src/ ./src/
"""

class MyApp(fal.App):
    image = ContainerImage.from_dockerfile_str(dockerfile_str)
```

See [custom container image docs](https://docs.fal.ai/serverless/development/use-custom-container-image#using-copy-and-add-commands).

**January 9, 2026**

## Skip retries for specific conditions

You can now skip retries for specific conditions using the `skip_retry_conditions` option.

```python theme={null}
class MyApp(fal.App):
    skip_retry_conditions=["timeout"]  # This app won't retry on timeout
    ...
```

Available conditions: `"server_error"`, `"timeout"`.

See [retry policy docs](https://docs.fal.ai/serverless/getting-started/core-concepts#retry-policy) for details.

**January 7, 2026**

## Graceful shutdown of fal apps

Starting with `fal>=1.61.0`, runners now receive a `SIGTERM` signal when terminated and are given a **5-second grace period** to complete ongoing requests before being forcefully terminated with `SIGKILL`.

This applies to all termination scenarios: expiration, manual stop/kill, and scaling down. Use the `teardown()` method to handle cleanup during this grace period.

See [lifespan docs](https://docs.fal.ai/serverless/getting-started/core-concepts#lifespan) for details.

**December 22, 2025**

## Add a health check endpoint to your application

Add a health check endpoint to your application to automatically replace unhealthy runners.

* **Health check endpoint** - Pass the `health_check` parameter to the `@fal.endpoint()` decorator to configure an endpoint as your health check
* **Periodic checks and recovery** - fal periodically (every 15 seconds) calls this endpoint and replace unhealthy runners if it fails for a few consecutive calls

Example:

```python theme={null}
class MyApp(fal.App):
    @fal.endpoint("/health", health_check=fal.HealthCheck(failure_threshold=3))
    def health(self) -> HealthResponse:
        if not self.connection.is_alive():
            raise RuntimeError("Lost connection to the external service")
        return HealthResponse(status="ok")
```

See [health check endpoint docs](https://docs.fal.ai/serverless/development/add-health-check-endpoint).

**December 22, 2025**

## Disable environment build cache

You can now disable the environment build cache
by passing the `--no-cache` flag to the `fal deploy` or `fal run` command.

See [custom container image docs](https://docs.fal.ai/serverless/development/use-custom-container-image#disable-build-cache).

**November 25, 2025**

## Scale your application with the new scaling delay feature

Scale your application with the new scaling delay feature.

* **Scaling delay** - the amount of seconds the system will wait for a request to be picked up by a runner before triggering a scale up of a runner

Example:

```python theme={null}
class MyApp(fal.App):
    scaling_delay = 30
    # ...
```

See [scaling docs](https://docs.fal.ai/serverless/deployment-operations/scale-your-application#scaling-delay).

**November 17, 2025**

## Reduce cold start times with shared compiled PyTorch caches

Dramatically reduce cold start times for torch.compile() models with the new inductor cache utilities.

* **Load pre-compiled CUDA kernels** in \~2 seconds instead of recompiling for 20-30 seconds on each worker
* **GPU-specific caching** automatically organized by GPU type (H100, H200, A100)
* **Two usage patterns**: Manual control with `load_inductor_cache()` / `sync_inductor_cache()` or automatic with `synchronized_inductor_cache()` context manager
* **Persistent shared storage** at `/data/inductor-caches/<GPU_TYPE>/<cache_key>.zip`
* First worker compiles and shares, subsequent workers load instantly

Example:

```python theme={null}
from fal.toolkit import synchronized_inductor_cache

with synchronized_inductor_cache("mymodel/v1"):
    self.model = torch.compile(self.model)
    self.warmup()  # Compilation happens once, synced automatically
```

See [compilation cache docs](https://docs.fal.ai/serverless/optimizations/optimize-startup-with-compiled-caches).

**November 14, 2025**

## Get Slack notifications for serverless app failures

Never miss critical issues with instant Slack alerts for your serverless applications.

* **Connect your workspace** with one-click OAuth installation
* **Choose notification channel** from a dropdown of your Slack channels
* **Instant alerts for**:
  * App startup failures and timeouts
  * Critical platform issues
  * Real-time error notifications
* **Team visibility** - everyone in the channel sees important updates
* Configure at `https://fal.ai/dashboard/notifications/settings`

**November 4, 2025**

## Stop and kill runners directly from the dashboard

No more switching to the CLI to manage your runners. You now have full lifecycle control right from the dashboard.

* **Graceful shutdown** or **force kill** runners with a single click
* Access at `https://fal.ai/dashboard/apps/{username}/{appname}/runners`

## Stream platform logs to your own endpoint with drains

Integrate fal's logging with your existing observability stack using the new Serverless Drains feature.

* **Automatic log forwarding** from apps, runners, and file operations in NDJSON format
* Works with Datadog, Splunk, Elasticsearch, or any HTTP endpoint
* Configure at `https://fal.ai/dashboard/drains`

**November 2, 2025**

## Upload larger files with improved timeout handling

We've significantly improved the reliability of file uploads from URLs, especially for large datasets and model files.

* **Extended timeout to 10 minutes** for `fal files upload` and `fal files upload-url`
* Upload multi-GB files without timeout errors
* See [`fal files` docs](https://docs.fal.ai/serverless/cli/files)

**November 1, 2025**

## Restart all runners without redeploying

Apply environment changes or recover from bad states instantly with the new `fal apps rollout` command.

* **Restart all runners** for an app without creating a new deployment
* **Graceful by default** (runners finish current requests) or use `--force` for immediate restart
* Pick up new secrets, environment variables, or clear memory issues
* See [`fal apps rollout` docs](https://docs.fal.ai/serverless/cli/apps/rollout)

## Stop specific runners without affecting others

Target individual runners for maintenance with graceful shutdown via `fal runners stop`.

* Stop specific runners without affecting others, useful for targeted maintenance
* See [`fal runners` docs](https://docs.fal.ai/serverless/cli/runners)

## Debug production runners with interactive shell access

Jump directly into any running container to troubleshoot issues in real-time with `fal runners shell`.

* **SSH-like access** to inspect files, environment variables, and dependencies
* Debug production issues without redeploying
* See [`fal runners shell` docs](https://docs.fal.ai/serverless/cli/runners#shell)

**October 31, 2025**

## See everything happening in your app with the events timeline

Complete activity history for runners, deployments, and config changes in one place.

* **Unified timeline** of runner events, deployments, and config changes
* Access at `https://fal.ai/dashboard/apps/{username}/{appname}/events`

**October 25, 2025**

## Get from zero to deployed in minutes with in-app onboarding

New interactive guide walks you through your first serverless deployment step-by-step.

* **Step-by-step walkthrough** from installation to deployment with copy-paste examples
* Access at `https://fal.ai/dashboard/serverless-get-started`

**October 22, 2025**

## Delete files from fal storage

Remove files and directories with the new `fal files rm` command.

* **Recursive deletion**: `fal files rm path/to/file-or-directory`
* See [`fal files` docs](https://docs.fal.ai/serverless/cli/files)

**October 21, 2025**

## Platform APIs v1 officially released

Programmatically manage your model deployments with the new Platform APIs.

* **Model discovery** - search and metadata retrieval for 600+ models
* **Pricing and cost estimation** - real-time pricing information
* **Usage tracking** - detailed line items with quantities and prices
* **Analytics** - request counts, error rates, and latency percentiles
* Available at `https://api.fal.ai/v1` - see [docs](https://docs.fal.ai/platform-apis)

## Get notified when you hit concurrent requests limits

Never wonder why requests are queuing—we now send notifications when you reach your concurrency limit.

* **Email and dashboard notifications** with smart throttling (immediate, 1h, 1d, weekly)
* **Limit value included** in 429 responses for programmatic handling

## Debug errors faster with the new errors page

Comprehensive error analytics to identify and resolve issues quickly.

* **Server vs client error rates** with 4xx/5xx breakdown and sparklines
* **Error timeline** with status code distribution and endpoint-level breakdown
* Access at `https://fal.ai/dashboard/apps/{username}/{appname}/errors`

**October 20, 2025**

## Stop or kill individual runners from the command line

Precise control over each runner's lifecycle without touching the dashboard.

* **`fal runners stop`** - gracefully stop a runner, allowing in-flight requests to complete
* **`fal runners kill`** - immediately terminate a runner without waiting
* See [`fal runners` docs](https://docs.fal.ai/serverless/cli/runners)

**October 16, 2025**

## See exactly how long runners spend starting up

Identify GPU availability bottlenecks and optimize cold start times.

* **Pending uptime metrics** show how long runners wait before becoming active
* Track PENDING, DOCKER\_PULL, and SETUP state durations separately

**October 15, 2025**

## Connect fal docs to Cursor with MCP

Access the complete fal documentation directly in Cursor using Model Context Protocol.

* **Complete documentation in your IDE** with AI-powered suggestions
* Simple setup: add fal MCP server to your `mcp.json` - see [guide](https://docs.fal.ai/model-apis/mcp)

## Personalized dashboard with creator and developer views

The dashboard now adapts to your workflow with two distinct experiences.

* **Creator view** - gallery-focused with favorite models and visual generation history
* **Developer view** - metrics-driven with usage stats, error tracking, and API analytics
* **Quick stats** showing credits, requests, and errors with sparklines

**October 13, 2025**

## Add custom headers to your API requests

Integrate seamlessly with analytics, auth, and middleware by passing custom HTTP headers.

* Add custom headers for analytics, authentication, or middleware integration
* Works with all client libraries

## Multi-GPU inference and training with fal.distributed

Scale AI workloads across multiple GPUs with the new `fal.distributed` module.

* **Data parallelism** - generate multiple outputs simultaneously (e.g., 4 images on 4 GPUs)
* **Model parallelism** - split large models across GPUs for faster generation
* **Distributed training** - synchronized gradient updates with DDP
* Supports 2, 4, or 8 GPU configurations on H100s and A100s
* See [distributed docs](https://docs.fal.ai/serverless/distributed/overview)

**October 10, 2025**

## Dedicated pages for Analytics, Runners, Logs, and Versions

Complete app details redesign gives each deployment aspect its own focused view.

* **New Analytics page** - runner-focused metrics with date range filtering
* **New Runners page** - app-scoped runner view with enhanced filters
* **New Logs page** - dedicated log viewer for debugging
* **New Versions page** - manage and view app revisions
* **Enhanced Overview** - endpoint stats and performance metrics at a glance

**October 9, 2025**

## Compare models side-by-side in the new Sandbox

Find the perfect model by testing multiple options in parallel with the same prompt.

* Run multiple models simultaneously with the same prompt
* Available at `https://fal.ai/sandbox`

**October 8, 2025**

## Manage deployments from Python without async/await

New synchronous client makes serverless management feel just like the CLI.

* Manage apps, runners, and deployments programmatically without async/await
* Same API as CLI: `client.apps.*`, `client.runners.*`, `client.deploy()`
* See [Python client docs](https://docs.fal.ai/serverless/python/client)

**October 6, 2025**

## Bring your own container to any deployment

Full control over your runtime environment with custom Docker images.

* Use `ContainerImage.from_dockerfile_str()` or `ContainerImage.from_dockerfile()`
* Install any dependencies, tools, or system packages you need
* See [custom containers guide](https://docs.fal.ai/serverless/development/use-custom-container-image)

**October 3, 2025**

## Dynamic auto-scaling with percentage-based buffers

Scale more intelligently by setting concurrency buffers as percentages instead of fixed numbers.

* Configure buffer as a percentage of current concurrency for dynamic scaling
* See [scaling docs](https://docs.fal.ai/serverless/deployment-operations/scale-your-application)

## Runner logs with streaming and filtering

Real-time log streaming and powerful filtering for faster debugging.

* **Stream logs in real-time** with `fal runners logs --follow`
* **Filter by time range** with `--since` and `--until`
* **Search logs** with `--search` parameter
* **Scrollable and searchable** in the dashboard with SSE-powered updates
* See [`fal runners logs` docs](https://docs.fal.ai/serverless/cli/runners#logs)

## Include local files in your deployments automatically

Bring configs, utilities, and code from your local machine into serverless apps.

* Specify files with relative or absolute paths to include at runtime
* Works with `fal run` and `fal deploy`
* See [app files docs](https://docs.fal.ai/serverless/development/app-files)

## Find what you need faster with reorganized navigation

Clearer dashboard structure groups features by workflow: Generate, Serverless, and Manage.

* **Generate** group: Sandbox, Model Gallery
* **Serverless** group: Apps, Logs, Files, Runners
* **Manage** group: Usage, Billing, API Keys, Webhooks, Team Members

**October 2, 2025**

## Know exactly which version each runner is running

Track deployments better with revision IDs shown on every runner.

* **Revision ID displayed** on runners to track which version is running
* **State renamed**: "DEAD" → "TERMINATED" for clarity

**October 1, 2025**

## Filter logs with custom labels and powerful queries

Find what you need instantly with EXACT/CONTAINS matching and multi-condition filters.

* **EXACT or CONTAINS** matching for label values
* **Multiple conditions** with OR logic (e.g., `status IN ["error", "warning"]`)
* Available in dashboard and API
* Examples: `error_type = "ValidationError"`, `endpoint CONTAINS "/api/v2/"`

## See what runners are doing during startup

Track exactly where runners are in the startup process—pending, pulling images, or setting up.

* `fal runners list` now shows PENDING, DOCKER\_PULL, and SETUP states
* Understand deployment progress in real-time

## View all app endpoints and config at a glance

Redesigned app details page surfaces the information you need most.

* Endpoints, configuration, and status all in one place

**September 27, 2025**

## Monitor and clear your request queue from the CLI

Check how many requests are queued and flush them when needed.

* `fal queue size app_name` - check queue size for an app
* `fal queue flush app_name` - flush all pending requests
* See [`fal queue` docs](https://docs.fal.ai/serverless/cli/queue)

**September 10, 2025**

## View runner history with time-based filtering

See terminated runners and filter by state to debug failures.

* `fal runners list --since "1h"` - view runners from the last hour (max 24h)
* `fal runners list --state dead` - filter by state (running, pending, setup, dead)
* Helpful for debugging failed deployments and understanding runner lifecycle
* See [`fal runners list` docs](https://docs.fal.ai/serverless/cli/runners#list)

**August 29, 2025**

## Reorganize files in fal storage without re-uploading

Move and rename files instantly with the new `fal files mv` command.

* Rename or move files in fal storage: `fal files mv source destination`
* See [`fal files` docs](https://docs.fal.ai/serverless/cli/files)

**August 26, 2025**

## See all your endpoint URLs immediately when testing

No more guessing which URL to use—CLI shows playground, sync, and async routes for every run.

* CLI prints playground, synchronous, and asynchronous routes for `fal run`
