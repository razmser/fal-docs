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

# Kotlin / Java Client

> fal client library for Android and JVM applications

The `fal-client` packages provide Kotlin and Java interfaces for calling fal AI models on Android and JVM platforms.

## Installation

```groovy Gradle (Kotlin) theme={null}
implementation 'ai.fal.client:fal-client-kotlin:0.7.1'
```

```groovy Gradle (Java) theme={null}
implementation 'ai.fal.client:fal-client:0.7.1'
```

```xml Maven (Kotlin) theme={null}
<dependency>
  <groupId>ai.fal.client</groupId>
  <artifactId>fal-client-kotlin</artifactId>
  <version>0.7.1</version>
</dependency>
```

```xml Maven (Java) theme={null}
<dependency>
  <groupId>ai.fal.client</groupId>
  <artifactId>fal-client</artifactId>
  <version>0.7.1</version>
</dependency>
```

> [!NOTE]
>
> **Java Async Support**
>
> If your code relies on asynchronous operations via `CompletableFuture` or `Future`, use the `ai.fal.client:fal-client-async` artifact instead.

## Quick Start

```kotlin Kotlin theme={null}
import ai.fal.client.kt

val fal = createFalClient()

val input = mapOf<String, Any>(
    "prompt" to "a cat",
    "seed" to 6252023,
    "image_size" to "landscape_4_3",
    "num_images" to 4
)
val result = fal.subscribe("fal-ai/flux/dev", input, options = SubscribeOptions(
    logs = true
)) { update ->
    if (update is QueueStatus.InProgress) {
        println(update.logs)
    }
}
```

```java Java theme={null}
import ai.fal.client.*;
import ai.fal.client.queue.*;

var fal = FalClient.withEnvCredentials();

var input = Map.of(
    "prompt", "a cat",
    "seed", 6252023,
    "image_size", "landscape_4_3",
    "num_images", 4
);
var result = fal.subscribe("fal-ai/flux/dev",
    SubscribeOptions.<JsonObject>builder()
        .input(input)
        .logs(true)
        .resultType(JsonObject.class)
        .onQueueUpdate(update -> {
            if (update instanceof QueueStatus.InProgress) {
                System.out.println(((QueueStatus.InProgress) update).getLogs());
            }
        })
        .build()
);
```

## Supported Platforms

* Android (API 21+)
* JVM (Java 11+)
* Kotlin Multiplatform (JVM target)

## API Reference

> [card] [Java API Reference](https://fal-ai.github.io/fal-java/fal-client/index.html)
>
> JavaDoc documentation

> [card] [Kotlin API Reference](https://fal-ai.github.io/fal-java/fal-client-kotlin/fal-client-kotlin/ai.fal.client.kt/index.html)
>
> KDoc documentation

> [card] [GitHub Repository](https://github.com/fal-ai/fal-java)
>
> Source code and examples
