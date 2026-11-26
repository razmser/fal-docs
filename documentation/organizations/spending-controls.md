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

> Set spend thresholds on API keys, teams, and members. Get email alerts and optionally lock requests automatically.

Spending Controls lets you set a threshold on how much your team spends in a period. When spend crosses the threshold, fal sends an email alert. You can also turn on auto-lock to block new requests. Use a threshold to watch spend for one API key, one member, or the team.

> [!NOTE]
>
> Spending Controls is available on enterprise plans. fal enables it on request. Contact your account team or [support@fal.ai](mailto:support@fal.ai).

A threshold counts model API usage only. This includes usage in the dashboard, such as the Playground. SDK and other charges do not count toward a threshold.

## Scope and Period

Each threshold applies to one of these scopes:

| Scope | What the threshold measures |
| :- | :- |
| **Team** | Spend from the team |
| **API key** | Spend from requests made with one API key |
| **Member** | Spend from one team member's logged-in usage, such as the Playground and Sandbox. It does not include requests made with API keys. |

On a personal account, the **Team** option is labeled **Account**. A personal account has no **Member** option.

Each threshold also has a period: **daily**, **weekly**, or **monthly**. Periods are calendar periods in UTC. A daily period resets at 12:00 AM UTC. A weekly period resets on Monday. A monthly period resets on the first day of the month.

Each team, API key, or member can have one threshold for each period. You cannot change the scope or period of a threshold after you create it. To change them, delete the threshold and create a new one.

## Manage Spending Controls

Open [Spending Controls](https://fal.ai/dashboard/usage-billing/spending-controls) in the dashboard (**Usage & Billing → Spending Controls**). Members with the [Admin or Billing role](managing-teams.md#team-roles) and the owner of a personal account can open it. When [Cross-Team Visibility](managing-teams.md#organization-policies) is on, members with the Admin or Billing role can open it for any team.

![The Spending Controls page with one monthly threshold on an API key and the Add alert and Add lock buttons](https://mintcdn.com/fal-d8505a2e/w5vRfVIivKAZIbVP/images/spending-controls/list-view.png?fit=max\&auto=format\&n=w5vRfVIivKAZIbVP\&q=85\&s=e80d98c9fa3dee18eb03a5feeccb3316)

## Create a Threshold

**1. Open the create panel**

On the Spending Controls page, select **Add alert**.

**2. Choose the subject**

Select **Team**, **API key**, or **Member**. Then pick the specific API key or member. If you work across teams with the Admin or Billing role, pick the team first. On a personal account, select **Account** or **API key**. fal sends alerts for a member threshold to the team admins, not to the member.

**3. Set the amount and period**

Enter an amount in USD that is greater than 0. Then select daily, weekly, or monthly.

**4. Choose the action**

Leave auto-lock off to receive email alerts only. Turn it on to also block new requests when spend crosses the threshold. If you do not see the auto-lock option, your team cannot use locks.

**5. Save**

Select **Create alert**.

![The Add spending alert panel with an API key, a weekly period, a threshold amount, and the auto-lock toggle turned on. Periods that already have a threshold are greyed out.](https://mintcdn.com/fal-d8505a2e/w5vRfVIivKAZIbVP/images/spending-controls/create-alert-auto-lock.png?fit=max\&auto=format\&n=w5vRfVIivKAZIbVP\&q=85\&s=fa40f5a5e187a61e9df1c77a871b22ec)

To review or change a threshold, expand its row. The row shows a cumulative spend chart for the current period and a **Controls** panel. In the panel, use the toggle to turn a threshold on or off. Use the **...** menu to edit or delete it.

![An expanded row for an API key threshold, showing the cumulative spend chart (empty, with no usage yet) and the Controls panel with a monthly \$500 threshold](https://mintcdn.com/fal-d8505a2e/w5vRfVIivKAZIbVP/images/spending-controls/api-key-alert.png?fit=max\&auto=format\&n=w5vRfVIivKAZIbVP\&q=85\&s=855db099ed441eb44dea8a4ed4881df7)

## Auto-Lock

When spend crosses a threshold that has auto-lock, fal blocks new API requests for that scope.

Auto-lock does not set an exact-dollar cap, for these reasons:

* A lock takes about one minute to take effect after spend crosses the threshold.
* A lock does not stop requests that are already running.

Because of this, spend can go above the threshold. Set the threshold below your real limit to leave room.

Keep these rules in mind:

* A threshold fires once per period. This applies to both the email alert and the auto-lock.
* Auto-lock does not fire while a manual lock is active on the same team, API key, or member.
* To lift a lock, select **Unlock** in the dashboard. Unlocking does not re-arm the threshold.

To let a threshold fire again in the same period:

1. If a lock is active, select **Unlock**.
2. Edit the threshold.
3. Turn on **Let this alert fire again this period**.

Otherwise, the threshold re-arms at the next period.

## Manual Locks

You can also lock a team, an API key, or a member by hand. On a personal account, you can lock the account or an API key. A manual lock blocks new requests for that scope.

To add a lock, select **Add lock** on the Spending Controls page. Then:

* Enter an internal note. The note is required and is visible to members with the Admin or Billing role.
* Choose when the lock ends. The default is the end of the month (UTC).

If you do not see **Add lock**, your team cannot use locks.

## Related

> [card] [Model Access Controls](access-controls.md)
>
> Restrict which models your team can call.

> [card] [Pricing](../model-apis/pricing.md)
>
> Learn how fal bills for usage.
