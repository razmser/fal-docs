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
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Publishing to the Marketplace

> Make your serverless app available on the fal Marketplace so anyone can call it with their own API key.

Every model on the [fal Marketplace](https://fal.ai/models) is a `fal.App` running on Serverless. You can publish your own app to the marketplace, making it callable by any fal user who authenticates with their own API key and pays for their own usage.

Publishing involves deploying your app in `shared` auth mode, defining billable units so callers are charged correctly, and working with the fal team to get listed. Before publishing, your app should be [deployed](../deployment/deploy-to-production.md) and stable, [scaled](../deployment/scaling-configuration.md) for your expected traffic, and have [health checks](../development/add-health-check-endpoint.md) and [analytics](observability/app-analytics.md) enabled.

## Set your app to shared mode

Shared mode means callers must authenticate with their own fal API key. Each caller is billed for their own usage — the app owner is not charged for caller compute.

```python theme={null}
class MyModel(fal.App):
    app_auth = "shared"
```

Or via the CLI:

```bash theme={null}
fal deploy my_app.py::MyModel --auth shared
```

> [!NOTE]
>
> Shared mode requires admin enablement on your account. Contact the fal team to get it enabled before deploying with `app_auth = "shared"`.

## Define billable units

When your app runs in shared mode, you control how callers are charged by setting the `x-fal-billable-units` response header. This tells the platform how many billing units to charge for each request.

```python theme={null}
response.headers["x-fal-billable-units"] = str(units)
```

The cost per unit is configured separately by the fal team in the billing system — your code only reports **how many units** each request consumed. If you don't set the header, the platform falls back to per-second billing based on the GPU machine type used.

Reported units change what a request costs only after the fal team configures a unit price for that endpoint. Until then the platform charges every request by duration at the machine rate, even when it carries the header or you report units for it. Ask the fal team to configure pricing when you are ready to test unit billing.

### Common patterns

**Per image (flat rate)**

Charge one unit per generated image, regardless of resolution:

```python theme={null}
response.headers["x-fal-billable-units"] = str(input.num_images)
```

**Per megapixel**

Scale the charge with output resolution. Higher resolutions cost proportionally more:

```python theme={null}
resolution_factor = math.ceil(
    (image_size.width * image_size.height) / (1024 * 1024)
)
response.headers["x-fal-billable-units"] = str(
    resolution_factor * input.num_images
)
```

**Per video second**

Charge based on the duration of generated video:

```python theme={null}
response.headers["x-fal-billable-units"] = str(video_duration_seconds)
```

**Per text chunk**

For text-based models (TTS, LLMs), scale with input length:

```python theme={null}
response.headers["x-fal-billable-units"] = str(max(1, len(prompt) // 1000))
```

**Flat per request**

Charge a fixed amount regardless of input or output:

```python theme={null}
response.headers["x-fal-billable-units"] = "1"
```

### Report units after the request

Some apps only know the unit count after the response has started. A streaming transcription counts audio seconds as they arrive, for example. For these apps, set the `x-fal-billable-units-webhook` header to `1` instead of a unit count.

```python theme={null}
response.headers["x-fal-billable-units-webhook"] = "1"
```

The platform then records no charge for the request and waits for your report. When the work is done, report the final unit count from your own backend:

```bash theme={null}
curl -X POST "https://rest.fal.ai/requests/billable-units/$REQUEST_ID" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"billable_units": 12.5}'
```

* `$REQUEST_ID` is the `x-fal-request-id` header that your app receives with the request. See [Request Headers](../development/request-headers.md).
* `$FAL_KEY` must be an API key of the account that owns the app. A key from another account gets a `403` response.
* The first report returns `200`. A second report for the same request returns `409`.
* The platform does not charge the caller for a request that ended with a 5xx status, even if you report units for it.

> [!WARNING]
>
> Report every request that you flag with `x-fal-billable-units-webhook`. The platform never charges a flagged request until your report arrives. This also applies to a WebSocket session that ends with a gateway error.

### WebSocket endpoints

A WebSocket endpoint sends one HTTP response, the `101` upgrade. Set the billing headers on that response. In a fal app, pass them to `accept()`:

```python theme={null}
import fal
from fastapi import WebSocket

@fal.endpoint("/transcribe", is_websocket=True)
async def transcribe(self, websocket: WebSocket) -> None:
    await websocket.accept(headers=[(b"x-fal-billable-units-webhook", b"1")])
    # ... stream audio, count seconds ...
```

Apps that run their own server set the same headers on the upgrade response. If the unit count is known when the connection opens, set `x-fal-billable-units` on the upgrade response instead. Most WebSocket apps count units during the session, so they use the webhook header and report the total when the session ends.

Without either header, the platform charges a WebSocket session by its duration at the machine rate. If a session ends with a gateway error, the platform ignores the unit count from the upgrade response and charges that session by duration. A session flagged with the webhook header still waits for your report.

Report each session once. A second report for the same request returns `409`, which means the platform already recorded the session, not that the report failed. The request's cost reflects your reported units only after a unit price exists for the endpoint.

## Get listed on the Marketplace

Once your app is deployed in shared mode with billable units configured, contact the fal team to get listed on [fal.ai/models](https://fal.ai/models). The team will configure:

* **Model card** — name, description, category, and example inputs/outputs
* **Pricing** — the cost per billable unit, visible on your model's page and at [fal.ai/pricing](https://fal.ai/pricing)
* **Visibility** — when and how your model appears in the marketplace

After listing, callers see your model's pricing on its page and the `X-Fal-Billable-Units` header in every response, so they can track their usage programmatically.

> [card] [Deploy to Production](../deployment/deploy-to-production.md)
>
> Deployment strategies and authentication modes.

> [card] [Model APIs Pricing](../model-apis/pricing.md)
>
> How marketplace model pricing works from the caller's perspective.
