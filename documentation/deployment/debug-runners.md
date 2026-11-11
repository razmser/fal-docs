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
> - [Deploy to Production](deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Debugging Runners

> Inspect live runners with an interactive shell or one-off commands using the fal CLI.

Sometimes logs and metrics aren't enough and you need to look inside a live runner -- inspect the filesystem, check GPU utilization, or verify that model weights downloaded correctly. The `fal runners` CLI gives you direct access to any running container.

First, find the runner you want to inspect:

```bash theme={null}
fal runners list
```

Then either open an interactive shell or run a one-off command.

## Interactive shell

[`fal runners shell`](../../api-reference/cli/runners.md#shell) opens an interactive session inside the runner's container, giving you full shell access to explore:

```bash theme={null}
fal runners shell runner_abc123xyz
```

Press `Ctrl+D` or type `exit` to disconnect. Use this for exploratory debugging sessions where you don't know exactly what you're looking for yet.

## One-off commands

[`fal runners exec`](../../api-reference/cli/runners.md#exec) runs a single command on the runner without opening a shell. Pass the command after a `--` separator so its arguments aren't parsed as `fal` CLI flags:

```bash theme={null}
# Verify model weights downloaded to persistent storage
fal runners exec runner_abc123xyz -- ls -lh /data/models

# Print environment variables on the runner
fal runners exec runner_abc123xyz -- env

# Tail a log file inside the container
fal runners exec runner_abc123xyz -- tail -f /var/log/my-app.log

# Start an interactive Python REPL (allocate a TTY with -it)
fal runners exec runner_abc123xyz -it -- python
```

Use `exec` for quick checks or scripted inspection; add `-it` when the command needs a TTY and stdin, like a REPL.

Both commands attach to the runner's container environment, so anything your app can see -- environment variables, mounted [persistent storage](../development/use-persistent-storage.md), GPU devices -- is available to you.

## Termination Reasons

When a runner begins shutting down, the dashboard shows a termination reason next to its state. Hover over the reason for a short explanation. Some reasons describe normal lifecycle activity, while others indicate an app or infrastructure problem that may need investigation.

The termination reason describes **why the runner stopped**. It does not, by itself, tell you whether a request was retried or which HTTP status code the caller received. A runner can stop after a request completed successfully, while a failed request can be retried on a different runner. See [Termination reasons, request outcomes, and retries](#termination-reasons-request-outcomes-and-retries) below for how these signals relate.

| Dashboard label | What it means | What to do |
| - | - | - |
| Runner crashed | The runner process stopped unexpectedly or could no longer be reached. An in-flight request might have been interrupted. | Check the runner logs for process exits, memory pressure, or other crashes. Correlate the runner ID with the request error type. |
| Repeated runner errors | Repeated request or release failures indicated that the runner was no longer healthy enough to reuse. | Review endpoint response codes and runner logs for a recurring failure. Do not return `503` for ordinary application errors. |
| Connection errors | fal repeatedly failed to establish or maintain a connection to the runner. | Check the runner logs and any custom networking or health-check configuration. Queue requests interrupted by a connection error are normally retried. |
| Request timed out | A request exceeded the app's `request_timeout`. The runner was stopped because the timed-out work might still have been running. | Review request duration and `request_timeout`. Queue requests are normally retried unless timeout retries are disabled. |
| Health check failed | The runner did not pass its configured or platform health check. | Verify the health-check path, method, start period, and app startup behavior. |
| Stop requested | A request outcome or runner state indicated that the runner should not be reused. This can also be requested with the `x-fal-stop-runner` response header. | Check the preceding request, response code and headers, and runner logs if the stop was unexpected. |
| Infrastructure maintenance | The runner's host was reclaimed, drained, or undergoing maintenance. | No action is normally required. fal replaces capacity automatically. |
| Unspecified platform request | An older or unspecified platform control request stopped the runner. | No action is normally required. Contact support if this happens repeatedly. |
| Stopped by user | A user stopped or killed the runner from the dashboard, CLI, or API. | Check team activity if the action was unexpected. |
| Stopped by fal | A fal administrator stopped or killed the runner. | Contact support if the action was unexpected. |
| Deployment rollout | A deployment rollout or configuration update replaced the runner. | No action is required unless the rollout was unexpected. |
| Capacity optimization | fal replaced the runner while consolidating workloads. | No action is required. |
| Infrastructure migration | fal replaced the runner while moving workloads between hosts or infrastructure pools. | No action is required. |
| Deployment closed | The runner's deployment or runner group was closed. | Confirm that the deployment or revision was not removed unexpectedly. |
| Scaled down | The app had more active runners than its configured `max_concurrency`. fal stopped this runner after its request was released to bring capacity back under the configured limit. | No action is normally required. Review recent `max_concurrency` changes and scaling settings if the stop was unexpected. |
| GPU limit exceeded | Keeping the runner active would have exceeded the configured GPU limit. | Review the app's GPU requirements and concurrency limits. |
| Keep-alive expired | The runner was not reusable after the app's `keep_alive` period expired. | This is expected for idle capacity. Increase `keep_alive` or `min_concurrency` if warm-start latency matters. |
| Internal release error | fal encountered an internal error while releasing the runner after a request. | Retry the request and contact support if the error recurs. |
| Unknown reason | fal could not determine why the runner stopped. | Check the runner logs and contact support with the runner ID if needed. |
| Reason unavailable | No termination reason was included in the runner data. | Check the runner logs and contact support with the runner ID if needed. |

New or unrecognized reasons are shown as received, with a generic explanation, so future backend values remain visible instead of being hidden.

### Termination reasons, request outcomes, and retries

A termination reason is recorded on a runner, while status codes, error types, and retry decisions belong to individual requests. There is not a one-to-one mapping between them:

* A normal lifecycle reason such as **Keep-alive expired**, **Deployment rollout**, or **Scaled down** usually happens after active work has finished and does not change a completed request.
* A failure reason such as **Runner crashed**, **Connection errors**, or **Request timed out** can identify why an attempt failed, but the request interface and retry configuration determine what happens next.
* A single runner can process many requests. The reason shown when it eventually stops does not describe every request that ran on it.
* Queue retries run the next attempt on another runner. Direct `run()` and `stream()` calls are not automatically retried.

#### Application response codes

Your endpoint's response code can affect both the current request and whether fal keeps the runner alive. The resulting termination label records the runner cleanup decision, not the response itself.

| Endpoint response | Runner behavior | Queue request behavior |
| - | - | - |
| `2xx` | Runner remains healthy. | Request succeeds; no retry. |
| `4xx` | Runner remains healthy. | Client error; no retry. |
| `500` or `502` | fal runs a health check. The runner stays active if it passes and is replaced if it fails. | Not retried by default. |
| `503` | Runner is immediately stopped because the response indicates it is unhealthy. | Retried on another runner, subject to the retry configuration and overall attempt limit. |
| `504` | fal runs a health check and may replace the runner if it fails. | Retried on another runner, subject to the retry configuration and overall attempt limit. |

Response headers can decouple these decisions. `X-Fal-Needs-Retry` controls whether the request is retried, while `x-fal-stop-runner` controls whether the runner is stopped. Therefore, neither the response code nor the termination reason alone is enough to reconstruct both decisions.

#### Platform failures

When fal detects a failure before receiving a usable endpoint response, the request interface determines the caller-visible result:

| Failure | Typical termination label | Queue request | Direct request |
| - | - | - | - |
| Runner process crashes or disconnects | **Runner crashed** or **Connection errors** | Retried on another runner. | Returns `503`. |
| App `request_timeout` is exceeded | **Request timed out** | Retried on another runner unless timeout retries are disabled. | Returns `504`. |
| Runner fails a health check after a response | **Health check failed** or another health-related reason | Depends on the response code or retry override header. | The original response is returned if one was received. |
| Runner is stopped for scaling, rollout, maintenance, or another lifecycle action | The corresponding lifecycle label | Normally unrelated to a completed request. If an attempt is interrupted, retry behavior follows the detected request failure condition. | Normally unrelated to a completed request. An interrupted call returns its detected error. |

For the complete retry controls, retry budgets, and response header behavior, see [Retries and Error Handling](../serverless/reliability/retries.md). To correlate a failed request with a runner, inspect the response's `error_type` field or `X-Fal-Error-Type` header and see [Request Error Types](../model-apis/request-errors.md).

> [card] [Understanding Runners](runners.md)
>
> What runners are, their lifecycle states, and how they start and shut down

> [card] [CLI: fal runners](../../api-reference/cli/runners.md)
>
> Full reference for list, logs, stop, kill, shell, and exec
