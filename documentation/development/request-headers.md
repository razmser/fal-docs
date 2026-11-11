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

# Request Headers

> Access request headers in your fal App for logging, tracing, and per-request logic.

For each request to your [fal App](https://fal.ai/docs/documentation/getting-started/apps-and-execution), fal adds headers such as the request ID and caller identity. The system also forwards custom headers from the caller. You can read these inside your endpoint handler via `self.current_request.headers.get()`.

Request headers are per-request context, complementing [environment variables](environment-variables.md) which are per-runner context. Use headers when you need to identify the specific request or caller (for logging, tracing, or per-user logic), and environment variables for runner-wide information like `FAL_APP_NAME` or `FAL_KEY`.

```python theme={null}
import fal
from pydantic import BaseModel

class Input(BaseModel):
    prompt: str

class MyApp(fal.App):
    machine_type = "GPU-A100"

    @fal.endpoint("/")
    def generate(self, input: Input) -> dict:
        request_id = self.current_request.headers.get("x-fal-request-id")
        caller_id = self.current_request.headers.get("x-fal-caller-user-id")

        print(f"Request {request_id} from {caller_id}")
        return {"result": "..."}
```

***

## Available Headers

| Header | Description |
| - | - |
| `x-fal-request-id` | Unique identifier for this request. Use for log correlation and debugging. |
| `x-fal-caller-user-id` | The caller's user ID (hashed for third-party apps). Useful for per-user logic. |
| `x-fal-cdn-token` | Token issued by fal for CDN downloads and uploads on behalf of the caller. Use `Authorization: Bearer <token>` for downloads. See [Serverless file handling](../serverless/data-retention.md#uploaded-and-generated-media). |
| `x-fal-endpoint` | The full endpoint path being called (e.g., `your-user/your-app/`). |

Custom headers passed by the caller are also forwarded to your app. Most headers that start with `x-fal-` are reserved for the gateway, and a caller cannot set them. The documented [platform headers](../model-apis/common-parameters.md) are the exception. A caller can set those, including [`X-Fal-Tags`](../model-apis/request-tags.md), but the gateway handles them and does not forward them to your app.

***

## Correlating with External Tools

### Sentry

Attach the request ID to Sentry events so you can find the exact fal request that caused an error:

```python theme={null}
import fal
from pydantic import BaseModel

class Input(BaseModel):
    prompt: str

class MyApp(fal.App):
    machine_type = "GPU-A100"
    requirements = ["sentry-sdk"]

    def setup(self):
        import sentry_sdk
        sentry_sdk.init(dsn="https://your-sentry-dsn")
        self.model = load_model()

    @fal.endpoint("/")
    def generate(self, input: Input) -> dict:
        import sentry_sdk
        sentry_sdk.set_tag("fal_request_id", self.current_request.headers.get("x-fal-request-id"))
        return {"image": self.model(input.prompt)}
```

### Structured Logging

Include the request ID in your log output for correlation with fal's built-in [logs](logging.md):

```python theme={null}
import fal
import logging
from pydantic import BaseModel

logger = logging.getLogger(__name__)

class Input(BaseModel):
    prompt: str

class MyApp(fal.App):
    machine_type = "GPU-A100"

    @fal.endpoint("/")
    def generate(self, input: Input) -> dict:
        rid = self.current_request.headers.get("x-fal-request-id")
        logger.info("Processing request", extra={"request_id": rid})

        result = self.model(input.prompt)

        logger.info("Request complete", extra={"request_id": rid})
        return {"image": result}
```

***

## Custom Headers

Callers can pass custom headers that are forwarded to your app:

```python theme={null}
# Caller passes a custom header
result = fal_client.subscribe("your-user/your-app", arguments={
    "prompt": "a sunset"
}, headers={
    "X-My-Trace-Id": "abc-123",
    "X-My-User-Tier": "premium"
})
```

```python theme={null}
# Your app reads it
class Input(BaseModel):
    prompt: str

@fal.endpoint("/")
def generate(self, input: Input) -> dict:
    trace_id = self.current_request.headers.get("x-my-trace-id")
    user_tier = self.current_request.headers.get("x-my-user-tier")
    ...
```

> [!NOTE]
>
> Header names are case-insensitive. `X-My-Header` and `x-my-header` both work.

## Response Header Size Limit

The total size of all response headers returned by your app is limited to **16 KB**. This includes platform headers injected by fal (like `x-fal-request-id` and `X-Fal-Billable-Units`) and any custom headers your app sets, such as billing units or routing hints from [`provide_hints()`](advanced/optimize-routing-behavior.md). If the combined headers exceed 16 KB, the response will fail.

Keep custom header values short. If you use `provide_hints()`, avoid returning large lists of model names or long strings — each hint value contributes to the total header size.

> [!NOTE]
>
> The system processes [platform headers](../model-apis/common-parameters.md) like `X-Fal-Request-Start-Timeout`, `X-Fal-No-Retry`, `X-Fal-Retry-Config`, `X-Fal-Store-IO`, and `X-Fal-Tags` before requests reach your runner. These controls are not available in `self.current_request.headers`. Your app receives the identity and CDN headers listed above, along with custom caller headers.

> [card] [Environment Variables](environment-variables.md)
>
> Per-runner context: FAL\_KEY, FAL\_APP\_NAME, FAL\_RUNNER\_STATE, and more
