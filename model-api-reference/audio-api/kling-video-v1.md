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

# Kling Video V1 API

> API reference for Kling Video V1. Generate speech from text prompts and different voices using the Kling TTS model, which leverages advanced AI techniques to create high-quality text-to-speech.

**Endpoint:** `POST https://fal.run/fal-ai/kling-video/v1/tts`
**Endpoint ID:** `fal-ai/kling-video/v1/tts`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v1/tts/playground)
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
    "fal-ai/kling-video/v1/tts",
    arguments={
        "text": "Hello world! Kling TTS is available on FAL!"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/kling-video/v1/tts", {
  input: {
      text: "Hello world! Kling TTS is available on FAL!"
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
  --url https://fal.run/fal-ai/kling-video/v1/tts \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "text": "Hello world! Kling TTS is available on FAL!"
}'
```

### Input Schema

**text** `string` *required*

The text to be converted to speech

**voice\_id** `VoiceIdEnum` (default: genshin\_vindi2)

The voice ID to use for speech synthesis Default value: `"genshin_vindi2"`

Possible values: `genshin_vindi2`, `zhinen_xuesheng`, `AOT`, `ai_shatang`, `genshin_klee2`, `genshin_kirara`, `ai_kaiya`, `oversea_male1`, `ai_chenjiahao_712`, `girlfriend_4_speech02`, `chat1_female_new-3`, `chat_0407_5-1`, `cartoon-boy-07`, `uk_boy1`, `cartoon-girl-01`, `PeppaPig_platform`, `ai_huangzhong_712`, `ai_huangyaoshi_712`, `ai_laoguowang_712`, `chengshu_jiejie`, `you_pingjing`, `calm_story1`, `uk_man2`, `laopopo_speech02`, `heainainai_speech02`, `reader_en_m-v1`, `commercial_lady_en_f-v1`, `tiyuxi_xuedi`, `tiexin_nanyou`, `girlfriend_1_speech02`, `girlfriend_2_speech02`, `zhuxi_speech02`, `uk_oldman3`, `dongbeilaotie_speech02`, `chongqingxiaohuo_speech02`, `chuanmeizi_speech02`, `chaoshandashu_speech02`, `ai_taiwan_man2_speech02`, `xianzhanggui_speech02`, `tianjinjiejie_speech02`, `diyinnansang_DB_CN_M_04-v2`, `yizhipiannan-v1`, `guanxiaofang-v2`, `tianmeixuemei-v1`, `daopianyansang-v1`, `mengwa-v1`

**voice\_speed** `float` (default: 1)

Rate of speech Default value: `1`

Range: `0.8` to `2`

### Output Schema

**audio** `File` *required*

The generated audio

### Input Example

```json theme={null}
{
  "text": "Hello world! Kling TTS is available on FAL!",
  "voice_id": "genshin_vindi2",
  "voice_speed": 1
}
```

### Output Example

```json theme={null}
{
  "audio": {
    "url": "https://v3.fal.media/files/monkey/O-ekVTtYqeDblD1oSf2uv_output.mp3"
  }
}
```

## Related

* [Kling AI Avatar v2 Pro](https://fal.ai/docs/model-api-reference/video-generation-api/kling-ai-avatar-v2-pro) — Video Generation
* [Kling AI Avatar v2 Standard](https://fal.ai/docs/model-api-reference/video-generation-api/kling-ai-avatar-v2-standard) — Video Generation
* [Kling AI Avatar](https://fal.ai/docs/model-api-reference/video-generation-api/kling-ai-avatar) — Video Generation
* [Kling AI Avatar Pro](https://fal.ai/docs/model-api-reference/video-generation-api/kling-ai-avatar-pro) — Video Generation

## Limitations

* `voice_speed` range: 0.8 to 2
