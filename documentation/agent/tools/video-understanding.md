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

# Video understanding and media download

> Ask fal Agent questions about a video with timestamps, and bring public media from YouTube, TikTok, or X into the chat.

## Understand a video

Attach a video, or refer to one in the chat, and ask a question. The agent watches the video and answers with timestamps.

```text theme={null}
Watch the hallway clip. Where does the product first appear, and does the logo stay readable?
```

Typical uses:

* **Review a generation.** Check continuity, motion quality, and whether the prompt was followed.
* **Find a moment.** Get the timestamp of a shot to trim, extend, or reference.
* **Describe a reference.** Turn an uploaded reference video into a shot list before recreating it.

You can narrow the question to a window, such as "between 0:10 and 0:25". A broad question on a long video takes several minutes.

| Limit | Value |
| :- | :- |
| Video size | 250 MB |
| Source | Videos hosted on fal. Uploads and generations qualify |
| Analysis timeout | 4 minutes |

## Download media

Give the agent a public link to a video, image, or audio file. The agent brings the file onto fal. You can then use it as a reference, an edit source, or a clip in a [video sequence](video-sequences.md).

```text theme={null}
Download this and use the first 3 seconds as the opening shot: https://www.youtube.com/watch?v=...
```

| Supported | Notes |
| :- | :- |
| YouTube | Public videos |
| TikTok | Public videos |
| X (Twitter) | Public posts with media |
| Direct file URLs | Any public `.mp4`, `.png`, `.mp3`, and similar |

Sites that require a login, such as Instagram and private posts, are not supported.

| Limit | Value |
| :- | :- |
| File size | 500 MB |
| Video resolution | Up to 1080p |
| Download timeout | 3 minutes |

Downloaded files are saved to your [Assets](../assets-and-characters.md) like any other input.
