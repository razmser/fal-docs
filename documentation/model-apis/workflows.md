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

# Workflow Endpoints: Chain Models in One API Call

> Chain several fal models into one endpoint. Stream submit, completion, and output events for each step, and give a step a fallback endpoint if it fails.

Workflows let you chain multiple models together into a single endpoint, creating complex pipelines that run as one API call. Instead of orchestrating individual model requests yourself, you define the steps and fal handles the execution, passing outputs from one model as inputs to the next.

Unlike standard model calls that return a single result, workflows emit streaming events as each step progresses, giving you access to intermediate results along the way. This makes them ideal for multi-step generation tasks where you want real-time feedback. For more on consuming streaming responses, see [streaming inference](inference/streaming.md).

## Workflow as an API

Workflow APIs work the same way as other model endpoints, you can simply send a request and get a response back. However, it is common for workflows to contain multiple steps and produce intermediate results, as each step contains their own response that could be relevant in your use-case.

Therefore, workflows benefit from the **streaming** feature, which allows you to get partial results as they are being generated.

If you submit a workflow to the [queue](inference/queue.md) and get the result with a [webhook](inference/webhooks.md), you do not get the streaming events. To get the endpoint and the request ID of each step in the result, see [Step request IDs](#step-request-ids).

## Workflow Events

The workflow API will trigger a few events during its execution, these events can be used to monitor the progress of the workflow and get intermediate results. Below are the events that you can expect from a workflow stream:

### The `submit` Event

This event is triggered every time a new step has been submitted to execution. It contains the `app_id`, `request_id` and the `node_id`.

```json theme={null}
{
  "type": "submit",
  "node_id": "stable_diffusion_xl",
  "app_id": "fal-ai/fast-sdxl",
  "request_id": "d778bdf4-0275-47c2-9f23-16c27041cbeb"
}
```

### The `completion` Event

This event is triggered upon the completion of a specific step.

```json theme={null}
{
  "type": "completion",
  "node_id": "stable_diffusion_xl",
  "output": {
    "images": [
      {
        "url": "https://fal.media/result.jpeg",
        "width": 1024,
        "height": 1024,
        "content_type": "image/jpeg"
      }
    ],
    "timings": { "inference": 2.1733 },
    "seed": 6252023,
    "has_nsfw_concepts": [false],
    "prompt": "a cute puppy"
  }
}
```

### The `output` Event

The `output` event means that the workflow has completed and the final result is ready.

```json theme={null}
{
  "type": "output",
  "output": {
    "images": [
      {
        "url": "https://fal.media/result.jpeg",
        "width": 1024,
        "height": 1024,
        "content_type": "image/jpeg"
      }
    ]
  }
}
```

### The `error` Event

The `error` event is triggered when an error occurs during the execution of a step. The `error` object contains the `error.status` with the HTTP status code, an error `message` as well as `error.body` with the underlying error serialized.

```json theme={null}
{
  "type": "error",
  "node_id": "stable_diffusion_xl",
  "message": "Error while fetching the result of the request d778bdf4-0275-47c2-9f23-16c27041cbeb",
  "error": {
    "status": 422,
    "body": {
      "detail": [
        {
          "loc": ["body", "num_images"],
          "msg": "ensure this value is less than or equal to 8",
          "type": "value_error.number.not_le",
          "ctx": { "limit_value": 8 }
        }
      ]
    }
  }
}
```

### The `recovery` Event

The workflow sends this event when a step fails and the workflow starts the [fallback](#fallbacks) of that step.

```json theme={null}
{
  "type": "recovery",
  "node_id": "node-k3TqP9xL2a",
  "action": "fallback",
  "reason": "server_error",
  "failed_app": "minimax/h3-max/reference-to-video",
  "app": "bytedance/seedance-2.5/reference-to-video",
  "outcome": "submitted"
}
```

* `reason` is the failure type that started the fallback: `server_error`, `policy`, or `forced`.
* `outcome` is `submitted` when the fallback started. It is `submit_failed` when fal could not start the fallback.
* After a `submitted` recovery event, a `submit` event gives the request ID of the fallback.

### The `warning` Event

The workflow sends this event when the configuration of a step has a problem, but the workflow can continue. For example, fal ignores a fallback configuration that it cannot use. The step then runs without a fallback.

```json theme={null}
{
  "type": "warning",
  "node_id": "node-k3TqP9xL2a",
  "message": "The fallback step has an invalid endpoint or trigger configuration and was ignored."
}
```

## Fallbacks

A step can have one fallback. If the step fails, fal runs the fallback endpoint for that step. The workflow then continues with the output of the fallback.

Add the fallback to the `config` of the node:

```json theme={null}
"node-k3TqP9xL2a": {
  "type": "run",
  "id": "node-k3TqP9xL2a",
  "app": "minimax/h3-max/reference-to-video",
  "depends": ["input"],
  "input": {
    "prompt": "$input.prompt",
    "reference_image_urls": ["$input.image_url"],
    "prompt_expansion_mode": "balanced"
  },
  "config": {
    "fallbacks": [
      {
        "endpoint": "bytedance/seedance-2.5/reference-to-video",
        "input": {
          "prompt": "$input.prompt",
          "image_urls": ["$input.image_url"]
        },
        "on": ["server_error", "policy"]
      }
    ]
  }
}
```

| Field | Description |
| - | - |
| `endpoint` | The endpoint that fal runs when the step fails. It can be the same endpoint as the step. |
| `input` | The input for the fallback endpoint. It uses the same `$` references as the input of the node. |
| `on` | Optional. The failure types that start the fallback. The default is `["server_error", "policy"]`. |

The failure types are:

* `policy`: the endpoint returned HTTP 422.
* `server_error`: the endpoint returned a different 4xx or 5xx status.

The fallback must give an output with the same fields that the next steps use. In the example, both endpoints return `video`, so a next step can use `$node-k3TqP9xL2a.video.url` for both.

Fallback rules:

* fal tries the fallback one time. If the fallback also fails, the workflow stops with an `error` event.
* fal does not start the fallback when the input of the step is not valid. For example, a `$` reference that does not resolve stops the workflow.
* If `fallbacks` has more than one step, fal ignores the configuration and sends a `warning` event. The same occurs when the fallback step is not valid.
* When a step has a fallback, fal disables the automatic [model fallbacks](inference/reliability.md#model-fallbacks) of the endpoint for that step.

### Test the Fallbacks

To test the fallbacks of a workflow, add `fal_fallbacks=1` to the request URL. fal then runs the fallback of each step that has one, instead of the endpoint of the step. The `reason` of the recovery event is `forced`.

```bash theme={null}
curl -X POST "https://queue.fal.run/workflows/<user>/<workflow>?fal_fallbacks=1" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a dancer on a rooftop at sunset", "image_url": "https://example.com/dancer.png"}'
```

## Step Request IDs

When you submit a workflow to the queue, the result and the webhook payload contain only the final output. To get the endpoint and the request ID of each step, send `X-Fal-Workflow-Envelope: true` with the request.

Use the request IDs to get the cost of each step from [billing events](../../platform-apis/v1/models/billing-events.md). You can also find each step in your request history.

```python Python theme={null}
handler = fal_client.submit(
    "workflows/<user>/<workflow>",
    arguments={"prompt": "a dancer on a rooftop at sunset"},
    webhook_url="https://your-server.com/webhook",
    headers={"X-Fal-Workflow-Envelope": "true"}
)
```

```javascript JavaScript theme={null}
const { request_id } = await fal.queue.submit("workflows/<user>/<workflow>", {
  input: { prompt: "a dancer on a rooftop at sunset" },
  webhookUrl: "https://your-server.com/webhook",
  headers: { "X-Fal-Workflow-Envelope": "true" },
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/workflows/<user>/<workflow>?fal_webhook=https://your-server.com/webhook" \
  -H "Authorization: Key $FAL_KEY" \
  -H "X-Fal-Workflow-Envelope: true" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a dancer on a rooftop at sunset"}'
```

The result then has two fields. `output` is the result that you get without the header. `nodes` has one entry for each step that fal submitted. The keys of `nodes` are the node IDs of your workflow.

```json theme={null}
{
  "output": { "video_url": "https://v3b.fal.media/files/.../final.mp4" },
  "nodes": {
    "node-Hq2xS8pRk1": {
      "endpoint": "fal-ai/nano-banana-pro/edit",
      "request_id": "01a1...a1"
    },
    "node-k3TqP9xL2a": {
      "endpoint": "bytedance/seedance-2.5/reference-to-video",
      "request_id": "01a1...b2",
      "fallback": {
        "action": "fallback",
        "reason": "server_error",
        "failed_endpoint": "minimax/h3-max/reference-to-video",
        "failed_request_id": "01a1...c3"
      }
    },
    "node-Wm8vR2cN5d": {
      "endpoint": "fal-ai/ffmpeg-api/merge-audio-video",
      "request_id": "01a1...d4"
    }
  }
}
```

| Field | Description |
| - | - |
| `endpoint` | The endpoint that ran the step. If the fallback ran, this is the fallback endpoint. |
| `request_id` | The request ID of the step. |
| `fallback` | Only present when the [fallback](#fallbacks) of the step ran. |
| `fallback.reason` | The failure type that started the fallback: `server_error`, `policy`, or `forced`. |
| `fallback.failed_endpoint` | The endpoint that failed. |
| `fallback.failed_request_id` | The request ID of the failed attempt. It is not present for a `forced` fallback, because the failed endpoint did not run. |

To get the total cost of a run:

1. Collect all request IDs in `nodes`. Include each `failed_request_id`.
2. Get the [billing events](../../platform-apis/v1/models/billing-events.md) with the `request_id` filter. One call accepts up to 50 request IDs.
3. Set `start` and `end` to cover the time of the run. If you do not set `start`, you get only the last 24 hours.
4. Add the cost of the billing events.

If a request ID is not in the billing events for that time window, fal did not charge for it per request. Steps that run on your own deployed apps are the exception. fal bills these steps by runner time, so billing events do not list them.

### When a Step Fails

If the workflow stops with an error, the error body also contains `nodes`. The HTTP status code does not change. The steps that were still running when the workflow stopped are also in `nodes`, because they continue to run.

```json theme={null}
{
  "type": "error",
  "node_id": "node-k3TqP9xL2a",
  "message": "Error while fetching the result of the request 01a1...b2",
  "error": { "status": 503, "body": { "detail": "..." } },
  "nodes": {
    "node-Hq2xS8pRk1": { "endpoint": "fal-ai/nano-banana-pro/edit", "request_id": "01a1...a1" },
    "node-k3TqP9xL2a": { "endpoint": "minimax/h3-max/reference-to-video", "request_id": "01a1...b2" }
  }
}
```

### Limits

* Without the header, the result does not change.
* The header does not change the streaming events. In a stream, use the `submit` and `recovery` events.
* `nodes` shows only the fallback that you configured. It does not show the retries that fal does on the platform.
* If the workflow stops without a result, for example after a timeout, you do not get `nodes`.

## Example

A cool and simple example of the power of workflows is `workflows/fal-ai/sdxl-sticker`, which consists of three steps:

**Step 1**

Generates an image using `fal-ai/fast-sdxl`.

**Step 2**

Remove the background of the image using `fal-ai/imageutils/rembg`.

**Step 3**

Converts the image to a sticker using `fal-ai/face-to-sticker`.

What could be a tedious process of running and coordinating three different models is now a single endpoint that you can call with a single request.

> [tab] Javascript
>
> ```js theme={null}
> import { fal } from "@fal-ai/client";
>
> const stream = await fal.stream("workflows/fal-ai/4x4-grid-images", {
> input: {
>   prompt: "a cute puppy, in the style of pixar animation",
> },
> });
>
> for await (const event of stream) {
> console.log("partial", event);
> }
>
> const result = await stream.done();
>
> console.log("final result", result);
> ```

> [tab] python
>
> ```python theme={null}
> import fal_client
>
> stream = fal_client.stream(
>     "workflows/fal-ai/4x4-grid-images",
>     arguments={
>         "prompt": "a cute puppy, in the style of pixar animation",
>     },
> )
> for event in stream:
>     print(event)
> ```

> [tab] python (async)
>
> ```python theme={null}
> import asyncio
> import fal_client
>
> async def main():
>     stream = await fal_client.stream_async(
>         "workflows/fal-ai/4x4-grid-images",
>         arguments={
>             "prompt": "a cute puppy, in the style of pixar animation",
>         },
>     )
>
>     async for event in stream:
>         print(event)
>
>
> if __name__ == "__main__":
>     asyncio.run(main())
> ```

> [tab] Swift
>
> > [!NOTE]
> >
> > **Coming soon**
> >
> > The Swift client does not support streaming yet.

## Type Definitions

Below are the type definition in TypeScript of events that you can expect from a workflow stream:

```ts theme={null}
type WorkflowBaseEvent = {
  type: "submit" | "completion" | "error" | "output" | "recovery" | "warning";
  node_id: string;
};

export type WorkflowSubmitEvent = WorkflowBaseEvent & {
  type: "submit";
  app_id: string;
  request_id: string;
};

export type WorkflowCompletionEvent<Output = any> = WorkflowBaseEvent & {
  type: "completion";
  app_id: string;
  output: Output;
};

export type WorkflowDoneEvent<Output = any> = WorkflowBaseEvent & {
  type: "output";
  output: Output;
};

export type WorkflowErrorEvent = WorkflowBaseEvent & {
  type: "error";
  message: string;
  error: any;
};

type FallbackReason = "server_error" | "policy" | "forced";

export type WorkflowRecoveryEvent = WorkflowBaseEvent & {
  type: "recovery";
  action: "fallback";
  reason: FallbackReason;
  failed_app: string;
  app: string;
  outcome: "submitted" | "submit_failed";
};

export type WorkflowWarningEvent = WorkflowBaseEvent & {
  type: "warning";
  message: string;
};
```

The result that you get when you send the `X-Fal-Workflow-Envelope: true` header:

```ts theme={null}
export type WorkflowNode = {
  endpoint: string;
  request_id: string;
  fallback?: {
    action: "fallback";
    reason: FallbackReason;
    failed_endpoint: string;
    failed_request_id?: string;
  };
};

export type WorkflowEnvelope<Output = any> = {
  output: Output;
  nodes: Record<string, WorkflowNode>;
};
```
