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
> - [Pricing](../pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Slack Notifications

> Receive real-time alerts in Slack when your apps fail to start.

Connect Slack to your fal account to receive notifications when your applications encounter startup issues. Get alerted immediately when a runner fails, so you can investigate before users are affected.

## Setting Up Slack

**1. Go to Notification Settings**

Navigate to [**Dashboard > Notifications > Settings**](https://fal.ai/dashboard/notifications/settings)

**2. Connect Slack**

Click **Add to Slack** to begin the OAuth flow. This installs the fal bot into your Slack workspace.

**3. Choose a Channel**

Select the Slack channel where you want to receive notifications. The fal bot will automatically join this channel.

> [!NOTE]
>
> Any authenticated team member can connect Slack to receive notifications.

## What You'll Be Notified About

### App Startup Timeout

Triggered when a runner's `setup()` method doesn't complete within the configured `startup_timeout`. The notification includes:

* App name and runner ID
* The timeout duration that was exceeded
* Link to logs for investigation
* Link to the startup timeout documentation

This typically indicates your model loading or initialization is taking too long. Consider optimizing your `setup()` method or increasing the `startup_timeout` value.

### App Startup Failure

Triggered when a runner's `setup()` method throws an error. The notification includes:

* App name and runner ID
* Link to logs to see the full error traceback

Common causes include missing dependencies, incorrect model paths, or out-of-memory errors during model loading.

### Queue Size Threshold

Triggered when the number of requests waiting in your app's queue exceeds a threshold over a time window. This usually means your app isn't scaling fast enough to meet demand, or callers are sending more traffic than your `max_concurrency` can handle.

### HTTP 5xx Threshold

Triggered when the number of HTTP 5xx responses from your app exceeds a threshold over a time window. A spike in server errors often points to a crashing endpoint, a bad deployment, or resource exhaustion.

## Per-App Notification Settings

You can configure alert thresholds for each app from the dashboard. These control when queue size and error rate notifications fire.

| Setting | Default | Description |
| - | - | - |
| `queue_size_threshold` | 100 | Number of queued requests that triggers an alert |
| `queue_size_threshold_duration` | 1h | Lookback window for queue size (e.g., `5m`, `1h`) |
| `http_5xx_threshold` | 100 | Number of 5xx responses that triggers an alert |
| `http_5xx_threshold_duration` | 1h | Lookback window for 5xx count (e.g., `5m`, `1h`) |

Set a threshold to `null` to disable that notification type. Lower durations (e.g., `5m`) make alerts more sensitive to short spikes; longer durations (e.g., `1h`) smooth out transient errors.

## Managing Your Connection

From the [Notification Settings](https://fal.ai/dashboard/notifications/settings) page you can:

* **View connection status** -- See if Slack is connected, and which workspace and channel are configured
* **Change channel** -- Switch notifications to a different Slack channel
* **Reconnect** -- If the bot token expires or the bot is removed from your workspace, reconnect with one click

## Troubleshooting

**Not receiving notifications?**

* Verify the fal bot is still in your selected channel (it may have been removed)
* Check that your Slack connection hasn't expired in the notification settings
* Ensure the app experiencing issues belongs to your team account

**Bot was removed from workspace?**

* Go to notification settings and click **Reconnect** to re-authorize the fal bot

> [card] [Monitor Performance](monitor-performance.md)
>
> View real-time metrics and logs in the fal dashboard
