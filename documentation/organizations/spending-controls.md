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

# Spending Controls

> Get an alert when usage costs cross a threshold, and block new requests to stop more costs.

Spending controls help you monitor and limit your usage costs on fal. There are two types of control.

* An **alert** notifies you when your usage costs cross a threshold in a daily, weekly, or monthly period.
* A **lock** blocks new requests. Requests that are already running continue to completion.

You can set a control on a whole team, on one API key, or on one team member. Members with the Admin or Billing role can manage spending controls. In an organization with cross-team visibility, these members can manage the controls for every team from one page.

> [!NOTE]
>
> fal must enable spending controls for your account. fal enables locks separately. Contact [support@fal.ai](mailto:support@fal.ai) to request access.

Open [Spending Controls](https://fal.ai/dashboard/usage-billing/spending-controls) from **Settings** > **Usage & Billing**.

![The Spending controls page with the Add alert and Add lock buttons](https://mintcdn.com/fal-d8505a2e/SyOpEnehPAp3oqf0/images/spending-controls/empty-state.png?fit=max\&auto=format\&n=SyOpEnehPAp3oqf0\&q=85\&s=8c1cbaa2bd7f4fed9c0e952592d7aa9f)

## Set Up an Alert

1. Click **Add alert**.
2. In an organization, select the team.
3. Select the subject. The subject is the whole team, one API key, or one member.
4. Select the period.
5. Enter the threshold amount.
6. Optional: turn on **Auto-lock** to block new requests when costs reach the threshold.
7. Click **Create alert**.

The chart in the panel shows the costs for the last two periods. Use it to compare the threshold with your usual costs.

![The Add spending alert panel with the Engineering team, a monthly period, and a \$5,000 threshold](https://mintcdn.com/fal-d8505a2e/SyOpEnehPAp3oqf0/images/spending-controls/add-alert-sheet.png?fit=max\&auto=format\&n=SyOpEnehPAp3oqf0\&q=85\&s=11288afbebc8f9afe919a9ac3ea60464)

The new alert shows in the list. The bar shows the percentage of the threshold that the subject uses in the current period.

![The Engineering team alert at 60% of its monthly threshold](https://mintcdn.com/fal-d8505a2e/SyOpEnehPAp3oqf0/images/spending-controls/alert-row.png?fit=max\&auto=format\&n=SyOpEnehPAp3oqf0\&q=85\&s=4bde10f108ed3a2cd02733897e80299f)

Alerts on a team or API key show in your dashboard notifications. fal emails an alert on one member to the members with the Admin role.

Periods reset at 12:00 AM UTC. Alerts count model API usage only.

## Set Up a Lock

1. Click **Add lock**.
2. In an organization, select the team.
3. Select the subject.
4. Enter an internal note. Members with the Admin or Billing role can see this note.
5. Select when the lock ends.
6. Click **Lock now**.

![The Add spending lock panel for the batch-jobs API key, with a note and an end-of-month end date](https://mintcdn.com/fal-d8505a2e/SyOpEnehPAp3oqf0/images/spending-controls/add-lock-sheet.png?fit=max\&auto=format\&n=SyOpEnehPAp3oqf0\&q=85\&s=9871c99eb3c23c5b6b6828f4fc94dfc5)

The page shows each lock that blocks requests.

![A lock on the batch-jobs API key that blocks new requests](https://mintcdn.com/fal-d8505a2e/SyOpEnehPAp3oqf0/images/spending-controls/active-lock.png?fit=max\&auto=format\&n=SyOpEnehPAp3oqf0\&q=85\&s=a6ce6d91204818b36593574ac3f6eaf3)

## Remove a Lock

To remove all locks, click **Unlock all**. To remove one lock, expand its row. Then click **Unlock**.
