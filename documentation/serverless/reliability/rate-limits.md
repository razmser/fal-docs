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

# Rate Limits for Runner and App Management

> Call limits for the CLI commands, SDK methods, and REST endpoints that list, inspect, shell into, and stop apps and runners.

fal limits how often you can call the operations that list, inspect, shell into, and stop your apps and [runners](../../deployment/runners.md). These limits protect the shared control plane. They do not apply to the requests that call your app endpoints.

## Limits

Each limit is a number of calls in a one-minute window. fal counts the calls from each client separately. When the window resets, you can make calls again.

| CLI command | Python SDK method | Limit (calls per minute) |
| - | - | - |
| [`fal apps list`](../../../api-reference/cli/apps/list.md) | `client.apps.list()` | 120 |
| [`fal apps runners`](../../../api-reference/cli/apps/runners.md) | `client.apps.runners()` | 300 |
| [`fal runners list`](../../../api-reference/cli/runners.md#list) | `client.runners.list()` | 120 |
| [`fal runners logs`](../../../api-reference/cli/runners.md#logs) | Not available | 120 |
| [`fal runners shell`](../../../api-reference/cli/runners.md#shell) / [`exec`](../../../api-reference/cli/runners.md#exec) | Not available | 90 |
| [`fal runners stop`](../../../api-reference/cli/runners.md#stop) | `client.runners.stop()` | 60 |
| [`fal runners kill`](../../../api-reference/cli/runners.md#kill) | `client.runners.kill()` | 300 |

The SDK methods belong to [`SyncServerlessClient`](../../../api-reference/python-sdk/client.md). A CLI command and its SDK method use the same limit. For example, `fal runners list` and `client.runners.list()` count toward one limit of 120 calls per minute.

`fal runners logs` gets the runner details one time before it shows logs. This call counts toward the `GET /runners/{runner_id}` limit in the REST API.

These REST API endpoints also have limits:

| REST endpoint | Limit (calls per minute) |
| - | - |
| `GET /runners/{runner_id}` | 120 |
| `GET /runners` | 120 |

## When You Exceed a Limit

fal rejects the call until the window resets. The error depends on the interface:

* **REST API.** The response has the status `429 Too Many Requests`.
* **CLI and Python SDK.** The call fails with the gRPC status `RESOURCE_EXHAUSTED`.

Both responses include the `x-ratelimit-reset` header. Its value is the number of seconds until the window resets. For gRPC calls, read this header from the trailing metadata.

The CLI and the SDK do not retry a rejected call. Make sure that your code checks for this error and tries the call again.

> [!WARNING]
>
> If fal rejects a `stop` or `kill` call, the runner continues to run.

## Stay Under the Limits

* Wait between calls when you poll. For example, call `client.apps.runners()` every few seconds, not in a loop with no delay.
* Use one list call, not one call for each runner. `client.apps.runners("my-app")` returns all runners of an app in one call.
* Spread large batches of `stop` or `kill` calls over time.
* After a rejected call, wait for the time in `x-ratelimit-reset`, and then try again.

This example kills a list of runners. When fal rejects a call, it waits for the window to reset and tries the call again:

```python theme={null}
import time

import grpc
from fal.api import SyncServerlessClient

client = SyncServerlessClient()


def reset_seconds(error: grpc.RpcError) -> float:
    for key, value in error.trailing_metadata() or ():
        if key == "x-ratelimit-reset":
            return float(value)
    return 5.0


def kill_with_retry(runner_id: str, attempts: int = 5) -> None:
    for attempt in range(attempts):
        try:
            client.runners.kill(runner_id)
            return
        except grpc.RpcError as error:
            if error.code() != grpc.StatusCode.RESOURCE_EXHAUSTED:
                raise
            if attempt == attempts - 1:
                raise
            time.sleep(reset_seconds(error) + 1)


for runner_id in ["runner-id-1", "runner-id-2"]:
    kill_with_retry(runner_id)
```
