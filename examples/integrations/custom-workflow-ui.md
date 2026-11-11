> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../documentation/quickstart.md) and the [Model APIs overview](../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Custom Workflow UI Tutorial

> Define a fal workflow as JSON that chains models, such as FLUX.1 [dev] and background removal. Find each model's fields, and run it with workflows/execute.

## How to create a custom workflow UI

If you want to create your custom workflow and execute it using the fal API, you need to create a json object that describes the workflow. You can use the following template to create your custom workflow. Basically, a workflow definition must have an input node, a fal model node, and an output node. The input node is the request to the fal API. The fal model node is the model that you want to use. You can add as many fal model nodes as you want. The output node is the response from the fal API.

```json theme={null}
{
  // Input node / Request
  "input": {
    "id": "input",
    "type": "input",
    "depends": [],
    "input": {
      "prompt": ""
    }
  },

  // A fal model node
  "node_1": {
    "id": "node_1",
    "type": "run",
    "depends": ["input"],
    // The app is the model endpoint id
    "app": "fal-ai/flux/dev",
    "input": {
      // The prompt value is coming from the Input node
      "prompt": "$input.prompt"
    }
  },

  // Another fal model node
  "node_2": {
    "id": "node_2",
    "type": "run",
    "depends": ["node_1"],
    // The app is the model endpoint id
    "app": "fal-ai/bria/background/remove",
    "input": {
      // The image_url value is coming from the "node_1" node
      "image_url": "$node_1.images.0.url"
    }
  },

  // Output node / Response
  "output": {
    "id": "output",
    "type": "display",
    "depends": ["node_2"],
    "fields": {
      "image": "$node_2.image"
    }
  }
}
```

## How to find model input and output fields

Every fal model has input and output fields. You can find the input and output fields using the following URL:

```bash theme={null}
https://fal.ai/api/openapi/queue/openapi.json?endpoint_id=[endpoint_id]
```

For example:

```bash theme={null}
https://fal.ai/api/openapi/queue/openapi.json?endpoint_id=fal-ai/flux/dev
```

## How to execute a custom workflow

You can execute a custom workflow using `workflows/execute` endpoint.

```js theme={null}
const stream = await fal.stream(`workflows/execute`, {
    input: {
        // The input to the workflow
        input: {
            prompt: "A beautiful sunset over a calm ocean"
        },
        // The workflow definition
        workflow: {
          "input": {
            "id": "input",
            "type": "input",
            "depends": [],
            "input": {
              "prompt": ""
            }
          },
          "node_1": {
            "id": "node_1",
            "type": "run",
            "depends": ["input"],
            "app": "fal-ai/flux/dev",
            "input": {
              "prompt": "$input.prompt"
            }
          },
          "node_2": {
            "id": "node_2",
            "type": "run",
            "depends": ["node_1"],
            "app": "fal-ai/bria/background/remove",
            "input": {
              "image_url": "$node_1.images.0.url"
            }
          },
          "output": {
            "id": "output",
            "type": "display",
            "depends": ["node_2"],
            "fields": {
              "image": "$node_2.image"
            }
          }
        },
    },
});

stream.on("data", (event) => {
  console.log(event);
});

const result = await stream.done();
```
