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

# Xai Tts API

> API reference for Xai Tts. Generate speech with expressive and realistic voices from xAI

**Endpoint:** `POST https://fal.run/xai/tts/v1`
**Endpoint ID:** `xai/tts/v1`

> [card] [Try it in the Playground](https://fal.ai/models/xai/tts/v1/playground)
>
> Run this model interactively with your own prompts.

### Quick Start

```python title="Python" theme={null}
import fal_client

def on_queue_update(update):
    if isinstance(update, fal_client.InProgress):
        for log in update.logs:
           print(log["message"])

result = fal_client.subscribe(
    "xai/tts/v1",
    arguments={
        "text": "Hello! This is xAI text to speech, brought to you by Fal AI."
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("xai/tts/v1", {
  input: {
      text: "Hello! This is xAI text to speech, brought to you by Fal AI."
    },
  logs: true,
  onQueueUpdate: (update) => {
    if (update.status === "IN_PROGRESS") {
      update.logs.map((log) => log.message).forEach(console.log);
    }
  },
});
console.log(result.data);
console.log(result.requestId);
```

```bash title="cURL" theme={null}
curl --request POST \
  --url https://fal.run/xai/tts/v1 \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "text": "Hello! This is xAI text to speech, brought to you by Fal AI."
}'
```

### Input Schema

**text** `string` *required*

The text to convert to speech. Maximum 15,000 characters. Supports speech tags for expressive delivery: inline tags like \[laugh], \[pause], \[sigh] and wrapping tags like \<whisper>text\</whisper>, \<slow>text\</slow>.

**voice** `VoiceEnum` (default: eve)

Voice to use for synthesis. eve: energetic, upbeat. ara: warm, friendly. rex: confident, clear. sal: smooth, balanced. leo: authoritative, strong. Default value: `"eve"`

Possible values: `eve`, `ara`, `rex`, `sal`, `leo`

**language** `LanguageEnum` (default: auto)

BCP-47 language code or 'auto' for automatic detection. Supported: en, zh, fr, de, hi, id, it, ja, ko, pt-BR, pt-PT, ru, es-MX, es-ES, tr, vi, bn, ar-EG, ar-SA, ar-AE. Default value: `"auto"`

Possible values: `auto`, `en`, `ar-EG`, `ar-SA`, `ar-AE`, `bn`, `zh`, `fr`, `de`, `hi`, `id`, `it`, `ja`, `ko`, `pt-BR`, `pt-PT`, `ru`, `es-MX`, `es-ES`, `tr`, `vi`

**output\_format** `OutputFormat`

Output format configuration. Defaults to MP3 at 24 kHz / 128 kbps.

### Output Schema

**audio** `File` *required*

The generated audio file.

### Input Example

```json theme={null}
{
  "text": "Hello! This is xAI text to speech, brought to you by Fal AI.",
  "voice": "eve",
  "language": "auto"
}
```

### Output Example

```json theme={null}
{
  "audio": {
    "url": "https://v3b.fal.media/files/b/0a92750b/exZJCm6TDejS5xIulJs2r_xai_tts_output.mp3"
  }
}
```

## Limitations

* `voice` restricted to: `eve`, `ara`, `rex`, `sal`, `leo`
