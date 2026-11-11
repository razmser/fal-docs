> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Logging

> Understand how logs are captured, scoped, and surfaced across runner logs, request logs, and the Playground.

fal captures your app's `stdout` and `stderr`, including `print()` statements, Python `logging` output, and error tracebacks.

Runner logs cover a runner's lifetime. Request logs cover a single request. For deployed apps, both are restricted to the app owner. Non-owner callers do not receive them.

The app owner is the personal or team account that owns the deployment.

## Runner Logs

Runner logs capture the full output of a [runner](../deployment/runners.md) process from the moment it starts until it shuts down. This includes output from `setup()` (model loading, initialization), every request the runner handles, and any background output between requests. Runner logs are visible only to the app owner through the [dashboard](https://fal.ai/dashboard/logs) and CLI.

Use runner logs for internal diagnostics: model loading times, memory usage, cache behavior, and anything that spans the runner's lifetime rather than a single request.

```python theme={null}
class MyApp(fal.App):
    def setup(self):
        print("Loading model weights...")  # This appears in runner logs
        self.model = load_model()
        print(f"Model loaded, VRAM: {torch.cuda.memory_allocated() / 1e9:.1f}GB")
```

You can view runner logs in the CLI with [`fal runners logs <runner-id>`](https://fal.ai/docs/reference/cli/runners), or in the [dashboard](https://fal.ai/dashboard/logs) filtered by runner ID.

## Request Logs

Request logs contain runner output emitted while a request is processing. Only the authenticated app owner can retrieve them through [queue status](../model-apis/inference/queue.md#check-status) or the Playground. Use `with_logs=True` in Python or `logs: true` in JavaScript to request them. Non-owner callers receive empty log arrays. Sharing a request link does not grant log access.

Apps with `max_multiplexing > 1` return no request logs, even to the owner. Concurrent requests share a runner's output. Use [runner logs](#runner-logs) to debug these apps.

```python theme={null}
import fal
from pydantic import BaseModel

class Input(BaseModel):
    prompt: str

class MyApp(fal.App):
    @fal.endpoint("/")
    def run(self, input: Input) -> dict:
        print(f"Generating for prompt: {input.prompt}")  # Owner-only request log
        result = self.model(input.prompt)
        print(f"Generation complete, inference time: {result.time:.2f}s")
        return {"image": result.image}
```

Owner-only access does not prevent log storage. Do not log secrets or customer data that must not be retained.

### Log Privacy Settings

Request logs are owner-only regardless of the **Log Privacy** setting.

Setting `private_logs=True` excludes app output from request-log responses, even for the owner. Logs are still captured. The owner can read them through the dashboard's runner logs and `fal runners logs`.

The [Log Privacy setting](https://fal.ai/dashboard/account/log-privacy) supplies the default for apps deployed without an explicit `private_logs` value. An explicit `True` or `False` overrides that default. Changing the account default does not change existing deployments. Redeploy an app to pick up the new default.

## Playground Logs

The [Playground](../model-apis/playground.md) follows the same [request-log rules](#request-logs) as the queue API. Non-owner callers receive no request logs. As the app owner, use runner logs if request logs are unavailable.

During development with [`fal run`](https://fal.ai/docs/reference/cli/run), logs also stream directly to your terminal, giving you immediate feedback without needing the dashboard.

## Writing Logs

For quick debugging, `print()` is the simplest approach. Both `stdout` and `stderr` are captured.

```python theme={null}
import fal
import sys

class MyApp(fal.App):
    @fal.endpoint("/")
    def run(self) -> dict:
        print("Processing request...")
        print("Warning: cache miss", file=sys.stderr)
        return {"message": "Hello, World!"}
```

For production apps, use Python's standard `logging` module to control log levels and message format. A good pattern is to route INFO and WARNING to `stdout` and ERROR and above to `stderr`, so you can filter by severity when reviewing logs.

```python theme={null}
import logging
import sys
import fal


def get_logger():
    stdout_handler = logging.StreamHandler(sys.stdout)
    stdout_handler.setLevel(logging.INFO)
    stdout_handler.addFilter(lambda record: record.levelno < logging.ERROR)

    stderr_handler = logging.StreamHandler(sys.stderr)
    stderr_handler.setLevel(logging.ERROR)

    logging.basicConfig(
        level=logging.INFO,
        format="%(asctime)s %(levelname)s %(name)s: %(message)s",
        handlers=[stdout_handler, stderr_handler],
    )
    return logging.getLogger("my_app")


class MyApp(fal.App):
    def setup(self):
        self.logger = get_logger()

    @fal.endpoint("/")
    def run(self) -> dict:
        self.logger.info("Started request processing")
        try:
            result = {"message": "Hello, World!"}
            self.logger.info("Request completed successfully")
            return result
        except Exception:
            self.logger.exception("Request failed")
            raise
```

> [!TIP]
>
> Include the request ID in your log output for easier debugging. You can access it via `self.current_request.request_id` in your endpoint handler. See [Request Headers](request-headers.md) for all available per-request context including caller identity and custom headers.

> [!WARNING]
>
> Avoid logging secrets, API keys, or large raw payloads. Log IDs, sizes, and high-level status instead.

## Log Sources

When filtering logs in the dashboard or CLI, the `source` field tells you where the logs came from:

* `run` - logs from runners created by [`fal run`](https://fal.ai/docs/reference/cli/run) during development
* `gateway` - logs from runners serving deployed apps in production
* `deploy` - logs emitted by the deployment process itself when using [`fal deploy`](https://fal.ai/docs/reference/cli/deploy)

## Where to View Logs

| What you need | Where to look |
| - | - |
| All logs for a specific runner | [Dashboard](https://fal.ai/dashboard/logs) filtered by runner ID, or [`fal runners logs <runner-id>`](https://fal.ai/docs/reference/cli/runners) in the CLI |
| Logs for a specific request (app owner only) | [Queue status API](../model-apis/inference/queue.md#check-status) with logs enabled. [Request-log restrictions](#request-logs) apply. |
| Real-time logs during development | Terminal output from [`fal run`](https://fal.ai/docs/reference/cli/run) |
| Real-time logs in the browser (app owner only) | [Dashboard logs page](https://fal.ai/dashboard/logs) streams runner logs in real-time. Playground shows request logs with the [restrictions above](#request-logs). |
| Logs filtered by deployment version | [Dashboard](https://fal.ai/dashboard/logs) filtered by version ID |
