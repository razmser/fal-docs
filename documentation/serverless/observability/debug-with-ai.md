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

# Debugging with AI

> Use the Platform MCP to investigate serverless incidents from your AI assistant — grounded in your real requests, logs, analytics, and runner state

Once the [Platform MCP](../../setting-up/platform-mcp.md) is connected, your AI assistant can investigate your serverless apps the way you would: find the failing request, read its logs, judge the blast radius, and correlate with deploys and runner state. This guide shows how to actually run those investigations — what to ask, what the assistant does under the hood, and how to read the results.

> [!NOTE]
>
> This page assumes the Platform MCP is already connected. If not, start with the [setup steps](../../setting-up/platform-mcp.md#setup) — it's one command per client.

## The Debugging Loop

Effective prompts map onto the same loop you'd follow in the dashboard. You don't need to name tools — the assistant picks them from the [available tool set](../../setting-up/platform-mcp.md#available-tools) — but knowing the loop helps you ask sharp questions and sanity-check the answers.

**1. Ground in a concrete request**

Everything starts from a real request. *"Show me the last 10 failed requests to my-app"* → the assistant calls `fetch_requests`, which returns request ids, status, and timings. Adding payloads (`expand=payloads`) shows what went in and out.

**2. Read the logs**

With a `request_id` in hand: *"read the logs for that request, with stack traces"* → `fetch_request_logs` with `traceback: true`. This is usually where the actual exception lives.

**3. Judge the blast radius**

One bad request and a 40% error rate are different problems. *"Is this widespread?"* → `get_analytics` over the incident window, comparing error counts (by type: startup, connection, timeout, runtime) against request volume.

**4. Ask what changed**

Most incidents start with a change. *"Did anything change around 14:00?"* → `fetch_events` (deploys, config changes, runner lifecycle) and `list_revisions` (who deployed what, when). A deploy boundary that lines up with the error spike usually *is* the answer.

**5. Check live state**

Warehouse data lags a few minutes; the live snapshot doesn't. *"What does the app look like right now?"* → `get_current_state` returns that app's current runner counts by state, concurrency, and queue size (like the other per-app tools, it takes the app's owner and name).

**6. Zoom out to capacity and cost**

For slow-burn problems rather than incidents: `get_runner_history` shows runner counts by state over time (capacity analysis), and `get_usage` breaks down compute spend by app (admin-scoped key required).

## Worked Example

> **"Requests to `my-org/my-app` started failing about an hour ago — figure out why."**

A typical investigation, as the assistant runs it:

1. `list_apps` — resolve the app and its endpoint ids (only needed once per conversation).
2. `fetch_requests` filtered to the last hour — finds 23 failed requests, all HTTP 500, starting at 13:42.
3. `fetch_request_logs` with `traceback: true` on one failing request — `CUDA out of memory` in the traceback.
4. `get_analytics` over the last 3 hours — runtime error count jumps from \~0 to \~40% of traffic at 13:42; latency percentiles unchanged before that.
5. `fetch_events` around 13:42 — a deployment event at 13:40.
6. `list_revisions` — revision `rev_abc123`, deployed at 13:40 by a teammate, with the deploy message "bump batch size".

Conclusion the assistant hands back: the 13:40 deploy raised the batch size past what the GPU can hold; roll back or reduce it. Every claim is traceable to a tool result — request ids, timestamps, and revision ids you can verify in the [dashboard](https://fal.ai/dashboard).

## Prompt Recipes

Copy, adapt, ask. Each maps to one or two tool calls.

### Incident Triage

* *"Why did request `req_...` to my-app fail?"* — logs + traceback for one request
* *"Is my-app having an incident right now, or was that one bad request?"* — analytics error rates vs volume, plus live state
* *"Compare error rates on my-app this hour vs the same hour yesterday"*

### Deploys and Changes

* *"Errors on my-app jumped Tuesday afternoon — did that start with a deploy?"* — revisions lined up against the error timeline
* *"What changed on my-app in the last 24 hours?"* — events: deploys, config changes, runner churn
* *"Who deployed the current revision of my-app and what was the deploy message?"*

### Capacity and Queues

* *"Requests to my-app are taking forever — are we capacity-bound?"* — queue size from live state vs runner history: a growing queue with flat runner counts means undercapacity; growing with traffic means a spike
* *"How did my-app's runners behave overnight — did we scale to zero and cold-start in the morning?"* — runner history + cold-boot metrics from analytics

### Cost

* *"How much did my serverless apps cost this week, broken down by app?"* — `get_usage` (key on the `BILLING`, `READONLY`, or `FULL` preset)
* *"Which machine types drive most of my spend?"*

### Storage

* *"List what's in my persistent storage under /data/models"* — `list_files`
* *"Read the config file my app wrote to /data/config.json"* — `read_file` (small text files)

### Beyond Serverless

The four discovery tools open the rest of the Platform API without dedicated tools:

* *"List my compute instances"* — `search_operations("compute instances")` → `invoke_operation`
* *"What API keys does my account have?"*
* *"Show my organization's usage this month"*

## Reading Results Correctly

A few properties of the underlying APIs matter when you interpret what the assistant reports:

* **Warehouse lag** — requests, analytics, and usage data can lag real time by a few minutes. Responses carry an `as_of` marker, and the server reminds the assistant of this — but if it reports "no traffic in the last 5 minutes" during a live incident, suspect ingestion lag, not zero traffic. `get_current_state` is the exception: it's real-time.
* **Truncation, not flooding** — large results (a noisy log search, a huge request list) are truncated with guidance to narrow the query. If an answer looks thin, ask the assistant to filter tighter (shorter window, specific request id, log level) rather than to retry.
* **Key permissions** — most tools work with any valid key; `get_usage` needs a key on the `BILLING`, `READONLY`, or `FULL` preset. Ownership scoping always applies: you only ever see your own account.
* **Read-only** — the assistant can observe but not act. Write operations (like flushing a queue) appear in the catalog but refuse to execute, so you can hand it an incident without worrying it will "fix" something.

## Pair It with the Run MCP

The [fal MCP](../../setting-up/mcp.md) (`mcp.fal.ai/mcp-relay`) is for *building with models* — search the catalog, check schemas, run inference. The Platform MCP is for *operating what you deployed*. Connected side by side, one conversation can go from "generate a test image against my endpoint" to "now tell me why that request was slow".

> [card] [Platform MCP setup](../../setting-up/platform-mcp.md)
>
> Connect Claude Code, Cursor, or any MCP client in one command

> [card] [Observability overview](monitor-performance.md)
>
> The same signals in the dashboard, CLI, and integrations
