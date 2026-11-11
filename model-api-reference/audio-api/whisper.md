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

# Whisper API

> API reference for Whisper. Whisper is a model for speech transcription and translation.

**Endpoint:** `POST https://fal.run/fal-ai/whisper`
**Endpoint ID:** `fal-ai/whisper`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/whisper/playground)
>
> Run this model interactively with your own prompts.

## Quick Start

```python title="Python" theme={null}
import fal_client

def on_queue_update(update):
    if isinstance(update, fal_client.InProgress):
        for log in update.logs:
           print(log["message"])

result = fal_client.subscribe(
    "fal-ai/whisper",
    arguments={
        "audio_url": "https://storage.googleapis.com/falserverless/model_tests/whisper/dinner_conversation.mp3"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/whisper", {
  input: {
      audio_url: "https://storage.googleapis.com/falserverless/model_tests/whisper/dinner_conversation.mp3"
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
  --url https://fal.run/fal-ai/whisper \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "audio_url": "https://storage.googleapis.com/falserverless/model_tests/whisper/dinner_conversation.mp3"
}'
```

## Capabilities

* Audio input
* Text prompt input

## API Reference

### Input Schema

**audio\_url** `string` *required*

URL of the audio file to transcribe. Supported formats: mp3, mp4, mpeg, mpga, m4a, wav or webm.

**task** `TaskEnum` (default: transcribe)

Task to perform on the audio file. Either transcribe or translate. Default value: `"transcribe"`

Possible values: `transcribe`, `translate`

**language** `Enum`

Language of the audio file. If set to null, the language will be
automatically detected. Defaults to null.

If translate is selected as the task, the audio will be translated to
English, regardless of the language selected.

Possible values: `af`, `am`, `ar`, `as`, `az`, `ba`, `be`, `bg`, `bn`, `bo`, `br`, `bs`, `ca`, `cs`, `cy`, `da`, `de`, `el`, `en`, `es`, `et`, `eu`, `fa`, `fi`, `fo`, `fr`, `gl`, `gu`, `ha`, `haw`, `he`, `hi`, `hr`, `ht`, `hu`, `hy`, `id`, `is`, `it`, `ja`, `jw`, `ka`, `kk`, `km`, `kn`, `ko`, `la`, `lb`, `ln`, `lo`, `lt`, `lv`, `mg`, `mi`, `mk`, `ml`, `mn`, `mr`, `ms`, `mt`, `my`, `ne`, `nl`, `nn`, `no`, `oc`, `pa`, `pl`, `ps`, `pt`, `ro`, `ru`, `sa`, `sd`, `si`, `sk`, `sl`, `sn`, `so`, `sq`, `sr`, `su`, `sv`, `sw`, `ta`, `te`, `tg`, `th`, `tk`, `tl`, `tr`, `tt`, `uk`, `ur`, `uz`, `vi`, `yi`, `yo`, `zh`

**diarize** `boolean` (default: false)

Whether to diarize the audio file. Defaults to false. Setting to true will add costs proportional to diarization inference time.

**chunk\_level** `ChunkLevelEnum` (default: segment)

Level of the chunks to return. Either none, segment or word. `none` would imply that all of the audio will be transcribed without the timestamp tokens, we suggest to switch to `none` if you are not satisfied with the transcription quality, since it will usually improve the quality of the results. Switching to `none` will also provide minor speed ups in the transcription due to less amount of generated tokens. Notice that setting to none will produce **a single chunk with the whole transcription**. Default value: `"segment"`

Possible values: `none`, `segment`, `word`

**batch\_size** `integer` (default: 64)

Default value: `64`

Range: `1` to `64`

**prompt** `string`

Prompt to use for generation. Defaults to an empty string. Default value: `""`

**num\_speakers** `integer`

Number of speakers in the audio file. Defaults to null.
If not provided, the number of speakers will be automatically
detected.

### Output Schema

**text** `string` *required*

Transcription of the audio file

**chunks** `list\<WhisperChunk\>`

Timestamp chunks of the audio file

**inferred\_languages** `list\<Enum\>` *required*

List of languages that the audio file is inferred to be. Defaults to null.

**diarization\_segments** `list\<DiarizationSegment\>` *required*

Speaker diarization segments of the audio file. Only present if diarization is enabled.

## Input Example

```json theme={null}
{
  "audio_url": "https://storage.googleapis.com/falserverless/model_tests/whisper/dinner_conversation.mp3",
  "task": "transcribe",
  "diarize": false,
  "chunk_level": "segment",
  "batch_size": 64,
  "prompt": "",
  "num_speakers": null
}
```

## Output Example

```json theme={null}
{
  "text": "María, ¿qué cenamos hoy? No sé, ¿qué cenamos? ¿Cenamos pollo frito o pollo asado o algo? Mejor a la plancha, quiero una salada. A la plancha, vale. Y hacemos una ensalada con tomate y esas cosas. Vale. Pues eso lo hacemos, ¿vale? Venga, vale.",
  "chunks": [
    {
      "text": ""
    }
  ],
  "diarization_segments": [
    {
      "speaker": ""
    }
  ]
}
```

## Limitations

* `task` restricted to: `transcribe`, `translate`
* `chunk_level` restricted to: `none`, `segment`, `word`
* `batch_size` range: 1 to 64
