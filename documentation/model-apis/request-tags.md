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

# Request Tags

> Attach your own key=value tags to fal requests and break usage and estimated cost down by them.

Request tags let you attach your own `key=value` labels to a request. fal stores the tags with the request, and reports usage and estimated cost against them. Use tags to attribute spend to your own dimensions, such as a team, an environment, or a feature.

Tags do not change how fal runs a request. They do not change the price, and the model does not receive them.

> [!NOTE]
>
> **Availability:** Tag reporting must be enabled for your account. Contact your account team or [support@fal.ai](mailto:support@fal.ai) to request access. Until fal enables it, you can send tags, but no report shows them.

***

## Send Tags

Send the `X-Fal-Tags` header on the request. Pack every tag into one header value and separate the tags with commas.

```
X-Fal-Tags: team=design,env=prod
```

```python Python theme={null}
result = fal_client.subscribe(
    "fal-ai/nano-banana-2",
    arguments={"prompt": "a sunset"},
    headers={"X-Fal-Tags": "team=design,env=prod"}
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset" },
  headers: { "X-Fal-Tags": "team=design,env=prod" }
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/nano-banana-2" \
  -H "Authorization: Key $FAL_KEY" \
  -H "X-Fal-Tags: team=design,env=prod" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset"}'
```

The header is the same for a [queue](inference/queue.md) submit, a [synchronous](inference/synchronous.md) run, and an HTTP [stream](inference/streaming.md).

***

## Format and Limits

| | |
| - | - |
| **Header** | `X-Fal-Tags` |
| **Format** | `key=value` tags, separated by commas |
| **Key** | Letters, digits, and `.`, `_`, `-`. fal lowercases the key, so `Team` and `team` are the same key. |
| **Value** | Printable ASCII. A value cannot contain a comma or a control character. |
| **Maximum tags** | 10 per request |
| **Maximum key length** | 64 characters |
| **Maximum value length** | 256 characters |
| **Maximum total size** | 1 KB of keys plus values |

fal splits each tag on the first `=` character, so a value can contain more `=` characters. fal also removes the spaces around a key and a value.

If you send the same key two times, the last value wins. The limits count every tag you send, before fal removes the duplicate keys.

***

## Reserved Keys and Values

Keys that start with `fal.` are reserved for fal. The gateway drops a reserved key from your input.

The value `*` and any value that starts with `(` and ends with `)`, such as `(untagged)`, are also reserved for fal. The gateway drops a tag that has one of these values.

A value with parentheses inside it, such as `Acme (EU)`, is valid.

***

## Invalid Tags

The gateway never fails a request because of a tag. It drops each tag that breaks a rule, and it keeps the rest. The gateway still runs a request that has no valid tag.

When the gateway drops any tag content, the response carries `X-Fal-Tags-Dropped: 1`. The header does not report which tag the gateway dropped or why. Check for this header while you introduce tagging.

The client `subscribe()` method does not return the response headers. To read the header, send the request with `curl -i` or with your own HTTP client.

***

## Activation

fal reports on activated tags only, and only for an account that has tag reporting enabled. fal can activate a tag key in two ways.

* **Value-level activation.** fal activates each `key=value` pair that you name. Your reports show only the activated values of the key. This is the default.
* **Key-level activation.** fal activates the key for all of its values. You can filter your reports by any value that you send under the key.

fal enables reporting and activates the tags for you. When you request access, tell your account team or fal [support](support.md) which tag keys and values to report on. For key-level activation, give the key and ask for all values.

fal keeps the tags you send before activation. Those tags appear in your reports after fal activates them, so you do not lose the history.

Keep to a small, stable set of keys, such as `env`, `team`, and `feature`. Each account has a cap on the number of activated keys, and a cap on the number of values per key. A key-level activation counts toward the key cap. The cap on values per key does not apply to it.

### Key-Level Activation

Use key-level activation for a key that has too many values to name one by one. An example is a `customer_id` key that holds a token for each of your customers.

Your reports do not group by a key-level key, because each value would split the report into too many rows. The reports show the key in these ways.

* **Usage & Billing page.** The card for the key shows the number of distinct values. It also shows the cost or requests with and without the key. The page does not list the values.
* **Filter by an exact value.** To see the usage of one value, type the full value in the **Tag** filter on the [Usage & Billing page](https://fal.ai/dashboard/usage-billing). The filter does not suggest values.
* **FOCUS export.** The `Tags` column does not include a key-level key. The cost of the tagged requests stays in the export.

Read these caveats before you ask for key-level activation.

* **fal does not check the values.** A person who can see your reports can filter by any value of the key. Use key-level activation only for a key whose values are all safe to report.
* **The activation is retroactive.** You can also filter by the values that you sent before the activation. You cannot exclude one value from a key-level activation.

***

## Where Tags Appear

After fal activates your tags, the tags appear in these places.

* **Usage.** Group and filter your usage by an activated tag on the [Usage & Billing page](https://fal.ai/dashboard/usage-billing) in the dashboard. You can filter by a [key-level](#key-level-activation) key, but you cannot group by it.
* **FOCUS export.** The [FOCUS](https://focus.finops.org/focus-columns/#tags) cost and usage report can fill its `Tags` column from your activated tags. Call the [FOCUS endpoint](../../platform-apis/v1/account/focus.md) with `source=tagged-estimate`. An organization root uses the same source on the [organization FOCUS endpoint](../../platform-apis/v1/organization/focus.md).
* **Billing events.** Call the [billing events endpoint](../../platform-apis/v1/models/billing-events.md) with `source=tagged-billed`. Each event has a `tags` object, and you can filter the events with `tag=key=value`.

The other FOCUS sources, `invoice` and `estimate`, leave the `Tags` column empty. The FOCUS report is an enterprise feature, so ask fal support to enable it for your account.

Recent usage shows up later in a tagged source than in its default source. For example, `tagged-estimate` is delayed relative to `estimate`, and `tagged-billed` is delayed relative to `billed`. The Usage & Billing page and the FOCUS export report estimated cost, not invoiced cost.

***

## Coverage

Tag reporting covers authenticated, per-request-billed calls to fal Model APIs. That is the traffic that produces a per-request cost.

These paths do not report tagged cost.

* **[Realtime](inference/real-time.md) and [WebSocket](inference/websockets.md) connections.** fal does not capture tags on a WebSocket connection.
* **Your own deployed apps.** fal captures the tags on the request. Per-request cost attribution for a deployed app is not available yet.

***

## Keep Tags Safe to Report

Never put personal data or a secret in a tag. Your usage reports and your FOCUS export show the tag values.

Replace a sensitive identifier with a token, and send the token as the tag value. fal keeps a request's tags for as long as it keeps the billing record for that request.

> [card] [Platform Headers](common-parameters.md)
>
> Every platform header you can set on a request, in one place
