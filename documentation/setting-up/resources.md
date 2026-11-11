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
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Libraries, APIs, and Community

> Client libraries, open-source packages, API references, and community links for building with fal.

This page collects everything you need beyond the core documentation: the open-source libraries you install, the API references you look up, and the community channels where you can get help. Whether you are calling models from Python or JavaScript, deploying your own with the [CLI](../../api-reference/cli.md), or integrating fal into a larger system with the [Platform APIs](../../api-reference/platform-apis.md), the links below will get you there.

All fal libraries are open source and available on GitHub. The platform itself offers a [model gallery](https://fal.ai/models), a [dashboard](https://fal.ai/dashboard) for managing your account, and a [sandbox](https://fal.ai/sandbox) for comparing models side by side. If you get stuck, the [Discord](https://discord.gg/fal-ai) community and [blog](https://blog.fal.ai) are good places to start.

## Open-Source Packages

| Package | Install | What it does | Source |
| - | - | - | - |
| `fal` | `pip install fal` | Deploy models with the serverless runtime, CLI (`fal deploy`, `fal run`), and toolkit (`Image`, `File`, `download_file`) | [fal-ai/fal](https://github.com/fal-ai/fal) (Apache-2.0) |
| `fal-client` | `pip install fal-client` | Call models from Python with `subscribe`, `submit`, `upload_file` | [fal-ai/fal](https://github.com/fal-ai/fal/tree/main/projects/fal_client) (Apache-2.0) |
| `@fal-ai/client` | `npm install @fal-ai/client` | Call models from JavaScript with the same API as the Python client, with TypeScript types | [fal-ai/fal-js](https://github.com/fal-ai/fal-js) (MIT) |
| `@fal-ai/server-proxy` | `npm install @fal-ai/server-proxy` | Server-side proxy for web apps that keeps your API key off the client (Next.js, Express) | [fal-ai/fal-js](https://github.com/fal-ai/fal-js) (MIT) |
| `flashpack` | `pip install flashpack` | Fast model loading with high-throughput tensor loading for PyTorch (up to 25 Gbps) | [fal-ai/flashpack](https://github.com/fal-ai/flashpack) (MIT) |

## Platform

> [card] [Model Gallery](https://fal.ai/models)
>
> Browse 1,000+ production-ready AI models ([docs](../model-apis/overview.md))

> [card] [Dashboard](https://fal.ai/dashboard)
>
> Manage apps, keys, billing, and monitoring ([analytics](../serverless/observability/app-analytics.md), [events](../serverless/observability/app-events.md), [errors](../serverless/observability/error-analytics.md))

> [card] [Sandbox](https://fal.ai/sandbox)
>
> Compare models side by side in your browser ([docs](../model-apis/sandbox.md))

> [card] [Status Page](https://status.fal.ai)
>
> Real-time platform health and incident history

## Documentation

The docs are organized into three main areas depending on how you use fal. Model APIs covers calling existing models, Serverless covers deploying your own, and Compute covers dedicated GPU instances for training or custom workloads.

> [card] [Model APIs](../model-apis/overview.md)
>
> Call 1,000+ models with simple API calls: inference, streaming, real-time

> [card] [Serverless](../serverless.md)
>
> Deploy your own models: environment setup, endpoints, scaling, observability

> [card] [Compute](../compute.md)
>
> Dedicated GPU instances with SSH access for training and custom workloads

> [card] [Examples](../../examples.md)
>
> Step-by-step tutorials for image, video, and audio generation

> [card] [Changelog](../../changelog.md)
>
> Platform updates and new features

> [card] [OpenAPI Spec](../../api-reference/platform-apis/openapi-schema.md)
>
> Machine-readable API specification for building custom integrations

## API References

For programmatic access beyond the client libraries, fal exposes a REST API for managing apps, files, and keys, a full CLI reference, and the Python SDK docs.

> [card] [Platform APIs](../../api-reference/platform-apis.md)
>
> REST API for managing apps, files, metrics, and keys

> [card] [CLI Reference](../../api-reference/cli.md)
>
> Full command reference for the fal CLI

> [card] [Python SDK](https://fal.ai/docs/api-reference/python-sdk)
>
> Python SDK API reference

## Client Libraries

fal has official client libraries for Python and JavaScript, plus community-maintained clients for Swift, Kotlin, and Dart. Each library handles authentication, request queuing, and file uploads so you can focus on your application.

> [card] [Python](../../api-reference/client-libraries/python.md)
>
> pip install fal-client

> [card] [JavaScript](../../api-reference/client-libraries/javascript.md)
>
> npm install @fal-ai/client

> [card] [Swift](../../api-reference/client-libraries/swift.md)
>
> iOS client

> [card] [Kotlin](../../api-reference/client-libraries/kotlin.md)
>
> Android / JVM client

> [card] [Dart](../../api-reference/client-libraries/dart.md)
>
> Flutter client

## Community and Support

The fal community is active on Discord, where you can ask questions, share projects, and get help from the team and other developers. The blog covers product updates and engineering deep dives.

> [card] [Discord](https://discord.gg/fal-ai)
>
> Join the community, ask questions, share what you're building

> [card] [Blog](https://blog.fal.ai)
>
> Product updates, tutorials, and engineering deep dives

> [card] [Contact Sales](https://fal.ai/enterprise#contact-sales)
>
> Enterprise plans, dedicated support, and custom solutions
