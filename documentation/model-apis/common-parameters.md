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

# Platform Headers

> HTTP headers that control request behavior across all inference methods on fal.

When you call a model or your own deployed app on fal, you can pass platform-level HTTP headers that control how the request is handled. These headers are separate from the model's input arguments (like `prompt` or `image_size`) and from SDK method parameters (like `start_timeout` or `client_timeout`). They apply at the infrastructure level -- controlling retries, payload storage, media expiration, and routing.

Some of these headers have dedicated SDK parameters that set them automatically. For example, passing `start_timeout=30` in the SDK sets `X-Fal-Request-Start-Timeout: 30` under the hood. Others, like `X-Fal-Store-IO`, can only be set via the `headers` dict. This page documents all platform headers in one place. For headers that have SDK parameters, the corresponding method pages are linked.

***

## X-Fal-Request-Start-Timeout (`start_timeout`)

Server-side **time-to-start** deadline in seconds. The clock starts when the request is submitted and covers queue wait, runner acquisition, and failed retry attempts. Once a runner successfully begins processing, the timeout stops and inference can run as long as it needs. If the deadline is reached before any runner starts processing, fal returns `504` with `X-Fal-Request-Timeout-Type: user`. To limit total client-side wait time (including processing), use `client_timeout` on [`subscribe()`](inference/queue.md#client_timeout-subscribe-only) instead.

| | |
| - | - |
| **Header** | `X-Fal-Request-Start-Timeout` |
| **Default** | No timeout |
| **Minimum** | > 0.1 seconds |
| **SDK parameter** | `start_timeout` on [`submit()`](inference/queue.md#start-timeout), [`subscribe()`](inference/queue.md#start-timeout), and [`run()`](inference/synchronous.md#start-timeout) |

***

## X-Fal-Runner-Hint (`hint`)

Routing hint that tells fal to try to route the request to a specific runner. Useful for session affinity -- for example, keeping requests pinned to a runner that already has a LoRA adapter or conversation state loaded in memory. If the hinted runner is unavailable, fal routes to any available runner.

| | |
| - | - |
| **Header** | `X-Fal-Runner-Hint` |
| **Default** | Automatic routing |
| **SDK parameter** | `hint` on [`submit()`](inference/queue.md#hint), [`subscribe()`](inference/queue.md#hint), and [`run()`](inference/synchronous.md#hint) |

***

## X-Fal-Queue-Priority (`priority`)

Queue priority for the request. Priority applies to the **per-user per-endpoint queue**. Therefore setting `priority="low"` on a shared model API (like `fal-ai/flux/dev`) deprioritizes your request relative to your other requests. Requests between users are fairly prioritized.

| | |
| - | - |
| **Header** | `X-Fal-Queue-Priority` |
| **Default** | `"normal"` |
| **Values** | `"normal"`, `"low"` |
| **SDK parameter** | `priority` on [`submit()`](inference/queue.md#priority) and [`subscribe()`](inference/queue.md#priority) |

***

## X-Fal-Object-Lifecycle-Preference

Control how long generated files are stored on fal's CDN and who can access them.

| | |
| - | - |
| **Header** | `X-Fal-Object-Lifecycle-Preference` |
| **Default** | Your account setting (forever and publicly readable if not configured) |
| **Format** | JSON: `{"expiration_duration_seconds": <seconds>, "initial_acl": {"default": "allow"\|"forbid"\|"hide", "rules": [...]}}` |
| **Fields** | `expiration_duration_seconds` -- see [Data Retention](media-expiration.md). `initial_acl` -- see [File Access Controls](file-access-controls.md). Either field is optional. |

```python Python theme={null}
import json

result = fal_client.subscribe(
    "fal-ai/nano-banana-2",
    arguments={"prompt": "a sunset"},
    headers={
        "X-Fal-Object-Lifecycle-Preference": json.dumps({
            "expiration_duration_seconds": 3600
        })
    }
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset" },
  headers: {
    "X-Fal-Object-Lifecycle-Preference": JSON.stringify({
      expiration_duration_seconds: 3600
    })
  }
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/nano-banana-2" \
  -H "Authorization: Key $FAL_KEY" \
  -H 'X-Fal-Object-Lifecycle-Preference: {"expiration_duration_seconds": 3600}' \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset"}'
```

> [card] [Data Retention & Storage](media-expiration.md)
>
> Full guide to media expiration, payload retention, and the delete API

> [card] [File Access Controls](file-access-controls.md)
>
> Restrict who can access files on the fal CDN with per-user access rules

***

## X-Fal-Store-IO

Prevent fal from retaining request payloads (JSON inputs and outputs) in request history. Payloads are stored for 90 days by default and power the request history in your dashboard. Your account team can configure a different account default. An explicit per-request header takes precedence.

| | |
| - | - |
| **Header** | `X-Fal-Store-IO` |
| **Default** | `"1"` (stored for 90 days, unless an account default is configured) |
| **Values** | `"0"` to disable storage |

```python Python theme={null}
result = fal_client.subscribe(
    "fal-ai/nano-banana-2",
    arguments={"prompt": "a sunset"},
    headers={"X-Fal-Store-IO": "0"}
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset" },
  headers: { "X-Fal-Store-IO": "0" }
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/nano-banana-2" \
  -H "Authorization: Key $FAL_KEY" \
  -H "X-Fal-Store-IO: 0" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset"}'
```

> [!NOTE]
>
> This controls request-history payload storage only. CDN files have separate retention controls. The model deployment owner controls application logging. See [Zero Data Retention (ZDR)](media-expiration.md#zero-data-retention-zdr) for retention controls and ZDR coverage. If you deploy your own apps, see [Serverless Data Retention & Storage](../serverless/data-retention.md).

***

## X-Fal-No-Retry

Disable automatic retries for this request. By default, queue-based requests are retried for up to 10 total attempts on server errors (503, 504, connection errors).

| | |
| - | - |
| **Header** | `X-Fal-No-Retry` |
| **Default** | Retries enabled |
| **Values** | `"1"`, `"true"`, `"yes"` to disable |

```python Python theme={null}
result = fal_client.subscribe(
    "fal-ai/nano-banana-2",
    arguments={"prompt": "a sunset"},
    headers={"X-Fal-No-Retry": "1"}
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset" },
  headers: { "X-Fal-No-Retry": "1" }
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/nano-banana-2" \
  -H "Authorization: Key $FAL_KEY" \
  -H "X-Fal-No-Retry: 1" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset"}'
```

> [card] [Reliability & Retries](inference/reliability.md)
>
> Learn more about automatic retries, fallbacks, and error handling

***

## X-Fal-Retry-Config

Set a separate retry limit for each retry condition, instead of the all-or-nothing `X-Fal-No-Retry` switch. The value is a JSON object that maps a retry condition (`server_error`, `timeout`, or `connection_error`) to an object with a `retries` count. `retries` is the number of **additional** attempts after the first, so `retries: 3` allows up to 4 total attempts for that condition. This header only works on your own apps -- it is ignored on shared or public model APIs.

| | |
| - | - |
| **Header** | `X-Fal-Retry-Config` |
| **Default** | Platform default retries per condition |
| **Values** | JSON object, e.g. `{"timeout": {"retries": 0}, "server_error": {"retries": 3}}` |

```python Python theme={null}
import json

result = fal_client.subscribe(
    "your-username/your-app-name",
    arguments={"prompt": "a sunset"},
    headers={
        "X-Fal-Retry-Config": json.dumps(
            {"timeout": {"retries": 0}, "server_error": {"retries": 3}}
        )
    }
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("your-username/your-app-name", {
  input: { prompt: "a sunset" },
  headers: {
    "X-Fal-Retry-Config": JSON.stringify({
      timeout: { retries: 0 },
      server_error: { retries: 3 },
    }),
  }
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/your-username/your-app-name" \
  -H "Authorization: Key $FAL_KEY" \
  -H 'X-Fal-Retry-Config: {"timeout": {"retries": 0}, "server_error": {"retries": 3}}' \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset"}'
```

> [card] [Reliability & Retries](../serverless/reliability/retries.md#per-request-client-side-control)
>
> Per-condition budgets, the overall attempt ceiling, and precedence rules

***

## x-app-fal-disable-fallback

Disable automatic model fallbacks for this request. By default, fal may reroute requests to equivalent alternative endpoints if the primary is unavailable.

| | |
| - | - |
| **Header** | `x-app-fal-disable-fallback` |
| **Default** | Fallbacks enabled |

```python Python theme={null}
result = fal_client.subscribe(
    "fal-ai/nano-banana-2",
    arguments={"prompt": "a sunset"},
    headers={"x-app-fal-disable-fallback": "true"}
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset" },
  headers: { "x-app-fal-disable-fallback": "true" }
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/nano-banana-2" \
  -H "Authorization: Key $FAL_KEY" \
  -H "x-app-fal-disable-fallback: true" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset"}'
```

> [card] [Reliability & Retries](inference/reliability.md)
>
> Learn more about model fallbacks

***

## X-Fal-Tags

Attach your own `key=value` tags to the request. Use tags to attribute spend to a team, an environment, or a feature. fal reports usage and estimated cost against the tags it activates for your account.

> [!NOTE]
>
> **Availability:** Tag reporting must be enabled for your account. Contact your account team or [support@fal.ai](mailto:support@fal.ai) to request access, and name the tag keys and values to activate.

Tag reporting covers per-request-billed calls to Model APIs. fal does not capture tags on a WebSocket connection, and it does not attribute per-request cost for your own deployed apps yet.

| | |
| - | - |
| **Header** | `X-Fal-Tags` |
| **Default** | No tags |
| **Format** | `key=value` tags, separated by commas, e.g. `team=design,env=prod` |
| **Limits** | 10 tags per request. Key 64 characters, value 256 characters, 1 KB in total. |

```python Python theme={null}
result = fal_client.subscribe(
    "fal-ai/nano-banana-2",
    arguments={"prompt": "a sunset"},
    headers={"X-Fal-Tags": "team=design,env=prod"}
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset" },
  headers: { "X-Fal-Tags": "team=design,env=prod" }
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/nano-banana-2" \
  -H "Authorization: Key $FAL_KEY" \
  -H "X-Fal-Tags: team=design,env=prod" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset"}'
```

> [card] [Request Tags](request-tags.md)
>
> The full rules, the activation step, and where tagged usage appears

***

## X-Fal-Workflow-Envelope

Add the endpoint and the request ID of each step to the result of a [workflow](workflows.md). The result becomes `{"output": ..., "nodes": ...}`. The webhook payload contains the same result. fal reads this header only for workflows.

| | |
| - | - |
| **Header** | `X-Fal-Workflow-Envelope` |
| **Default** | `"false"` (the result contains only the output) |
| **Values** | `"true"` |

```python Python theme={null}
handler = fal_client.submit(
    "workflows/<user>/<workflow>",
    arguments={"prompt": "a sunset"},
    headers={"X-Fal-Workflow-Envelope": "true"}
)
```

```javascript JavaScript theme={null}
const { request_id } = await fal.queue.submit("workflows/<user>/<workflow>", {
  input: { prompt: "a sunset" },
  headers: { "X-Fal-Workflow-Envelope": "true" },
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/workflows/<user>/<workflow>" \
  -H "Authorization: Key $FAL_KEY" \
  -H "X-Fal-Workflow-Envelope: true" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset"}'
```

> [card] [Step request IDs](workflows.md#step-request-ids)
>
> The result format, the error body, and how to get the cost of each step

***

## fal\_max\_queue\_length

Reject the request with `429` if the endpoint's queue already has more than this many requests waiting (across all callers). Useful for latency-sensitive applications that prefer to fail fast rather than wait in a long queue.

| | |
| - | - |
| **Query param** | `fal_max_queue_length` |
| **Default** | No limit |
| **Type** | Integer |

> [!NOTE]
>
> This parameter is passed as a query parameter on the URL, not as a header. The SDKs do not currently expose it as a named parameter; use the raw URL approach or pass it via `headers`.

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/nano-banana-2?fal_max_queue_length=10" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset"}'
```

***

## Response Headers

These headers are returned by fal in the response. They are informational; you don't set them.

> [!WARNING]
>
> The total size of all response headers is limited to **16 KB**. This includes both platform headers (listed below) and any custom headers set by the app. If the combined headers exceed 16 KB, the response will fail. This is most relevant when apps set large custom headers — for example, verbose routing hints via [`provide_hints()`](../development/advanced/optimize-routing-behavior.md).

| Header | Description |
| - | - |
| `x-fal-request-id` | Unique identifier for the request. Use this when contacting [support](support.md) or correlating logs. |
| `X-Fal-Billable-Units` | Billing units charged for this request. See [Pricing](pricing.md) for how units map to cost. |
| `X-Fal-Served-From` | Internal identifier of the runner that served the request. |
| `X-Fal-Request-Timeout-Type` | Set to `user` when your [`start_timeout`](#x-fal-request-start-timeout-start_timeout) deadline triggered the 504. See [Timeouts and Retries](../serverless/reliability/retries.md#timeouts-and-retries). |
| `X-Fal-Error-Type` | Error category on failure responses (e.g., `request_timeout`, `startup_timeout`, `runner_disconnected`). See [Request Error Types](request-errors.md). |
| `x-fal-runner-hints` | Routing hints returned by the runner for sticky session routing. See [Optimize Routing Behavior](https://fal.ai/docs/documentation/serverless/optimizations/optimize-routing-behavior.md). |
| `X-Fal-Tags-Dropped` | Set to `1` when fal dropped some [`X-Fal-Tags`](#x-fal-tags) content. Absent when fal kept every tag. |

> [card] [Common Model Arguments](model-arguments.md)
>
> Common input parameters like seed, image\_size, and safety checker that appear across many models
