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
> - [Pricing](../../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Web search and URL reading

> fal Agent can search the public web and read pages in full to ground its work in current information.

The agent can look things up. Give it a URL, or ask a question that needs current information, and it searches or reads the page before it generates.

```text theme={null}
Read https://example.com/brand and match the palette in the next batch.
```

## Web search

The agent runs a web search and reads the top results. It can restrict the search to a category, such as company, news, people, or financial reports. It can also limit results to pages published after a date.

* Up to 10 results per search.
* Posts on X (Twitter) are not accessible through search. Use [Download media](video-understanding.md#download-media) for a public X video.

## URL reading

Paste a URL in the composer, and the agent can read the page. It reads up to five pages at a time. Each page is read up to about 8,000 characters, so very long articles are read in part. Ask the agent to focus on a section when a page is long.

## Safety

Content from the web is treated as data, not as instructions. A page that contains instructions for an AI agent does not change the agent's behavior. The agent reports what the page says and continues with your request.
