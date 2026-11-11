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

# Documentation

> Learn how to build and deploy AI applications with fal

# Build with fal

The generative media platform powering the world's top AI apps.

Call **1,000+ optimized models** through a unified API, or deploy your own on the same infrastructure. Image, video, audio, music, speech, 3D, and real-time streaming. Built to scale to billions of requests.

Search or ask...

99.99%+Uptime

Billions+Requests/day

1,000+Endpoints

![Build with fal](https://mintcdn.com/fal-d8505a2e/QFO2a4qD7yLWEJFa/images/Hero_image_light.png?fit=max\&auto=format\&n=QFO2a4qD7yLWEJFa\&q=85\&s=be84ce69a57abe69927c7453e497768f)

![Build with fal](https://mintcdn.com/fal-d8505a2e/QFO2a4qD7yLWEJFa/images/Hero_image_dark.png?fit=max\&auto=format\&n=QFO2a4qD7yLWEJFa\&q=85\&s=c818dcf171b90cd6b31c46254c72e333)

> [card] [Model APIs](documentation/model-apis/overview.md)
>
> **Call 1,000+ models with one API.** Image, video, audio, and multimodal generation. Optimized and production-ready.

> [card] [Serverless](documentation/serverless.md)
>
> **Deploy your own models.** Same infrastructure, same autoscaling, same reliability. From zero to thousands of GPUs.

> [card] [Compute](documentation/compute.md)
>
> **Dedicated GPU instances.** Full SSH access for training, fine-tuning, and persistent workloads.

> [card] [fal Agent](documentation/agent.md)
>
> **Your creative partner across every model.** Plan, generate, and edit images, video, audio, and 3D in one conversation.

> [card] [Platform APIs](api-reference/platform-apis.md)
>
> REST APIs for model metadata, pricing, usage tracking, logs, files, and metrics

## Start with a Model API Call

Most users start here. Pick a model from the [Marketplace](https://fal.ai/models), get an [API key](https://fal.ai/dashboard/keys), and make a request. Three lines of code, no infrastructure to manage.

```python Python theme={null}
import fal_client

result = fal_client.subscribe("fal-ai/nano-banana-2", 
    arguments={"prompt": "a sunset over mountains"}
)
print(result["images"][0]["url"])
```

```javascript JavaScript theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset over mountains" }
});
console.log(result.data.images[0].url);
```

```bash cURL theme={null}
curl -X POST https://queue.fal.run/fal-ai/nano-banana-2 \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset over mountains"}'
```

Every model supports [synchronous and async queue](documentation/model-apis/inference.md) calls out of the box. Many also support [streaming](documentation/model-apis/inference/streaming.md) and [real-time WebSocket](documentation/model-apis/inference/real-time.md) connections. You can compare models side-by-side in the [Sandbox](https://fal.ai/sandbox) before committing to one.

> [card] [Model APIs Quickstart](documentation/model-apis/overview.md)
>
> Browse models, see pricing, and start generating

***

## Deploy Your Own Models

For teams that need to run custom models, proprietary pipelines, or fine-tuned variants, [fal Serverless](documentation/serverless.md) lets you deploy on the same engine that powers the Marketplace. Your apps run on [serverless GPUs](https://fal.ai/serverless) that scale with your traffic. fal has been running this infrastructure for over 3 years, and every model on the platform goes through the same lifecycle below.

**1. Develop**

A [fal.App](documentation/development/app-setup.md) is a Python class where your `setup()` method runs once per [runner](documentation/deployment/runners.md) to load model weights and initialize resources. Your [`@fal.endpoint`](documentation/development/handle-inputs-and-outputs.md) methods then serve incoming [requests](documentation/deployment/requests.md) using the initialized state. You declare hardware needs and [environment](documentation/development/container-setup.md) alongside your code, so infrastructure is versioned with your app.

```python theme={null}
import fal
from pydantic import BaseModel

class Input(BaseModel):
    prompt: str

class MyModel(fal.App):
    machine_type = "GPU-H100"
    
    def setup(self):
        self.model = load_my_model()
    
    @fal.endpoint("/")
    def generate(self, input: Input):
        return self.model(input.prompt)
```

**2. Test**

[`fal run`](documentation/development/getting-started/quick-start.md) spins up a cloud GPU [runner](documentation/deployment/runners.md) and gives you a temporary URL so you can test on the same hardware you'll use in production. It also generates a [playground UI](documentation/model-apis/playground.md) automatically. For CI, [`AppClient`](documentation/development/test-models-and-endpoints.md) lets you run tests against ephemeral deployments.

```bash theme={null}
fal run my_app.py
```

**3. Deploy**

[`fal deploy`](documentation/deployment/deploy-to-production.md) creates a persistent, authenticated endpoint with autoscaling and built-in [retries](documentation/serverless/reliability/retries.md). Every deploy creates a new revision for instant [rollbacks](documentation/deployment/rollbacks.md). For staging and production separation, fal supports [multiple environments](documentation/deployment/manage-environments.md) per app.

Open the persistent app in the [Apps dashboard](https://fal.ai/dashboard/apps). Follow [Testing Your Own Apps](documentation/model-apis/playground.md#your-own-apps) to test its endpoints in the browser.

```bash theme={null}
fal deploy my_app.py
```

**4. Observe**

The [dashboard](https://fal.ai/dashboard) gives you real-time logs, request-level [analytics](documentation/serverless/observability/app-analytics.md), and [error tracking](documentation/serverless/observability/error-analytics.md) out of the box. Trace individual requests, spot latency regressions, and monitor runner utilization. For external stacks, fal supports [Prometheus metrics](documentation/serverless/observability/exporting-metrics.md) and [log drains](documentation/serverless/observability/log-drains.md) to any HTTPS endpoint.

**5. Scale**

fal scales [runners](documentation/deployment/runners.md) from zero to thousands of GPUs based on demand, with a multi-layer [caching](documentation/deployment/caching.md) system that reduces cold starts over time. [Scaling parameters](documentation/deployment/scale-your-application.md) let you control the tradeoff: `min_concurrency` keeps runners warm, `max_concurrency` caps spend, and `concurrency_buffer` pre-warms ahead of spikes. See [optimizing cold starts](documentation/serverless/optimizations/optimize-cold-starts.md) and [machine types](documentation/deployment/machine-types.md) for latency-sensitive workloads.

```python theme={null}
class MyModel(fal.App):
    min_concurrency = 2
    max_concurrency = 100
    concurrency_buffer = 3
```

**6. Distribute**

Endpoints start as private. You can deploy in `public` mode for open access, or [`shared` mode](documentation/deployment/deploy-to-production.md) where callers pay for their own usage. To list on the [Marketplace](https://fal.ai/models) for broader distribution and revenue, see [publishing to the marketplace](documentation/serverless/publishing-to-marketplace.md).

```python theme={null}
class MyModel(fal.App):
    app_auth = "shared"
```

> [card] [Serverless Quickstart](documentation/development/getting-started/quick-start.md)
>
> Deploy your first model in minutes

***

## Train Your Own Models

For training runs, fine-tuning, and workloads that need sustained GPU access, [fal Compute](documentation/compute.md) gives you dedicated instances with full SSH control. No cold starts, no autoscaling, just raw GPU power billed at a fixed hourly rate.

> [card] H100 SXM
>
> Single-GPU instances for development, fine-tuning, and single-GPU training

> [card] 8x H100 SXM
>
> Multi-GPU instances connected over InfiniBand for distributed training

| | Compute | Serverless |
| :- | :- | :- |
| **Best for** | Training, fine-tuning, batch jobs | API endpoints, on-demand inference |
| **Billing** | Per-hour, fixed rate | Per-second of execution |
| **Scaling** | Manual | Automatic |
| **Access** | Full SSH | Managed runners |

> [card] [Compute Quickstart](documentation/compute/quickstart.md)
>
> Provision your first GPU instance in minutes
