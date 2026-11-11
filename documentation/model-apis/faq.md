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

# FAQ

> Answers to common fal Model APIs questions about rate limits, failed request charges, credit expiry, file storage and URLs, commercial use, and browser calls.

Common questions about using fal. If you can't find the answer here, join our [Discord community](https://discord.gg/fal-ai) or [contact support](mailto:support@fal.ai).

> [accordion] Is there a rate limit?
>
> Yes. Every account has a [concurrency limit](concurrency-limits.md) that controls how many requests can be processed at the same time. New accounts start at 2 concurrent requests. As you purchase credits, the limit increases automatically up to 40. Requests that exceed your limit are queued and processed as slots free up. For higher limits, [contact sales](https://fal.ai/enterprise#contact-sales).

> [accordion] Do you charge for failed requests?
>
> Server errors (HTTP 500+) are never charged. If a runner fails to process your request due to an infrastructure issue, you pay nothing. Client-side errors like invalid inputs (HTTP 422) may still be charged if a runner spent GPU time processing the request before the error was detected.

> [accordion] Do my credits expire?
>
> Purchased credits expire 365 days from the date of purchase. Free credits and coupons have variable expiration depending on the specific grant, ranging from 1 week to 1 year. Check the [billing dashboard](https://fal.ai/dashboard/billing) for your current credit balance and expiration dates.

> [accordion] Do I pay for cold starts?
>
> No. For Model API endpoints, you are billed only for inference time. Cold start time, including container pull and model loading, is not charged.

> [accordion] What happens when my credits run out?
>
> When your credit balance drops below your account's lock threshold, your account is locked and API requests will be rejected. Add credits from the [billing dashboard](https://fal.ai/dashboard/billing) to unlock your account. Enterprise customers on invoice-based billing are not subject to automatic locking.

> [accordion] How long are generated files stored?
>
> Generated media files (images, videos, audio) are stored on the [fal CDN](fal-cdn.md) and available for at least 7 days by default. You can control the retention period per request using the `X-Fal-Object-Lifecycle-Preference` header. See [Data Retention](media-expiration.md) for details. Download and store any files you need to keep long-term.

> [accordion] Are generated file URLs public?
>
> By default, yes. Media URLs returned by fal (`https://v3b.fal.media/...`) are publicly accessible — anyone with the URL can access the file until it expires. To restrict access, set an ACL on the request so the files are private to your account, shared with specific users, or hidden; see [File Access Controls](file-access-controls.md). You can also download files to your own infrastructure if you prefer to manage access there.

> [accordion] Can I use generated files for commercial purposes?
>
> Each model has its own license. Most models on fal are available for commercial use and are marked with a `Commercial use` badge on the model page. Models marked `Research only` are restricted to non-commercial use. Check the model's page for its specific license.

> [accordion] Can I call fal from the browser?
>
> Yes, but you should not expose your API key in client-side code. Set up a [server-side proxy](inference/proxy-setup.md) that forwards requests to fal from your backend, keeping your key secure.

> [accordion] Can I deploy my own models?
>
> Yes. [fal Serverless](../serverless.md) lets you deploy your own models and applications on fal's GPU infrastructure. You can use any Python framework, bring your own Docker image, and scale automatically. See the [quickstart](../development/getting-started/quick-start.md) to get started.

> [accordion] Why was my request rejected with a content policy violation?
>
> fal applies content filters on certain models. If your request triggers a `content_policy_violation` error, the input was flagged by automated safety systems. This is a non-retryable 422 error. See the [error reference](errors.md) for details on this and other error types.

> [accordion] What is the Partner API?
>
> Models marked `Partner API` are hosted by fal's partners, and their availability is managed by the partner. Standard percentage discounts do not apply to Partner API models. However, fal Agent Pro and Max subscription discounts do apply when you use Partner API models through the fal UI, Sandbox, Playground, or CLI. Direct API usage remains pay as you go.

> [accordion] Can I switch to invoice-based billing?
>
> Yes. Invoice-based billing is available for higher-volume customers. [Contact sales](https://fal.ai/enterprise#contact-sales) with details about your expected usage.

> [accordion] Do you offer volume discounts?
>
> Yes. [Contact sales](https://fal.ai/enterprise#contact-sales) with information about your expected volume to discuss custom pricing.

> [accordion] When logging in with GitHub, I never receive the one-time code
>
> The one-time code is sent to the primary email on your GitHub account. If you no longer monitor that email, check [GitHub's documentation](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-email-preferences/changing-your-primary-email-address) to find or update it.
