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

# Get Your API Key

> Create an API key to authenticate your requests to fal

Most requests to fal require authentication. You need an API key to call any model on the [Model APIs](../model-apis/overview.md), deploy your own with [Serverless](../serverless.md), or access any private or shared endpoint. The only exception is endpoints deployed with `app_auth = "public"`, which accept unauthenticated requests.

API keys are tied to accounts, not people. If you are working under a [team](teams.md), the key belongs to that team and all members share it. The [fal client libraries](../model-apis/inference/client-setup.md) read your key automatically from the `FAL_KEY` environment variable, so once it is set, every SDK call and CLI command authenticates without extra configuration.

## Create Your API Key

**1. Go to the Dashboard**

Navigate to [fal.ai/dashboard/keys](https://fal.ai/dashboard/keys)

**2. Generate a Key**

Click **Create Key** and give it a name you'll remember

**3. Copy Your Key**

Copy the key immediately. You will not be able to see it again

## Set Your API Key

Set your key as an environment variable:

```bash macOS/Linux theme={null}
export FAL_KEY="your-api-key-here"
```

```bash Windows (PowerShell) theme={null}
$env:FAL_KEY="your-api-key-here"
```

```bash .env file theme={null}
FAL_KEY=your-api-key-here
```

Or configure it directly in code:

```python Python theme={null}
import fal_client

# The client automatically reads FAL_KEY from environment
# Or you can set it explicitly:
import os
os.environ["FAL_KEY"] = "your-api-key-here"
```

```javascript JavaScript theme={null}
import { fal } from "@fal-ai/client";

fal.config({
  credentials: "your-api-key-here",
});
```

> [!TIP]
>
> **Best practice**: Use environment variables instead of hardcoding keys in your code. This keeps your keys secure and makes it easy to use different keys for development and production.

## Key Permissions

When you create a key, you select a preset that controls what the key can reach. The `API` preset covers calls to any model on fal, including Model APIs and your own deployed endpoints. Most [Platform APIs](../../api-reference/platform-apis.md) also accept it. The `SERVERLESS` preset adds the CLI operations that [Serverless](../serverless.md) deployments need, such as `fal deploy` and `fal run`. If no preset matches your case, you select the exact permissions the key needs.

See [Key permissions](../model-apis/authentication/key-based.md#permissions) for the full preset table and the custom policy rules.

> [!NOTE]
>
> If you are not sure which preset to select, start with `API`. You can create a
> second key with more permissions when you need one.

## Test Your Key

Verify your key works by making a simple API call:

```bash theme={null}
curl -X POST "https://fal.run/fal-ai/flux/schnell" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a cute cat"}'
```

If you see a response with an image URL, your key is working correctly.

## Team Keys

If you're part of a team, make sure to select your team in the top-left corner of the dashboard before creating a key. Keys are scoped to the account (personal or team) that created them.
