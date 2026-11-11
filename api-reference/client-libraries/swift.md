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

# Swift Client

> fal client library for iOS, macOS, tvOS, and watchOS

The `fal-swift` package provides a native Swift interface for calling fal AI models on Apple platforms.

## Installation

Add the package via Swift Package Manager:

```swift theme={null}
.package(url: "https://github.com/fal-ai/fal-swift.git", from: "0.5.6")
```

## Quick Start

```swift theme={null}
import FalClient

let result = try await fal.subscribe(
    to: "fal-ai/flux/dev",
    input: [
        "prompt": "a cat",
        "seed": 6252023,
        "image_size": "landscape_4_3",
        "num_images": 4
    ],
    includeLogs: true
) { update in
    if case let .inProgress(logs) = update {
        print(logs)
    }
}
```

## Supported Platforms

* iOS 16+
* macOS 13+
* tvOS 16+
* watchOS 9+

## API Reference

> [card] [Swift API Reference](https://swiftpackageindex.com/fal-ai/fal-swift/documentation/falclient)
>
> Full API documentation on Swift Package Index

> [card] [GitHub Repository](https://github.com/fal-ai/fal-swift)
>
> Source code and examples
