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

# fal profile

> Use fal profile to manage several fal accounts on one machine. Create profiles, set a key and host for each, list them, choose the default, and delete them.

### Managing Profiles

The `fal` CLI allows you to manage multiple profiles, making it easy to switch between different fal accounts. This is particularly useful if you have multiple environments or projects.

#### Adding a New Profile

To add a new profile, set it as the default and then add the key:

```sh theme={null}
❯ fal profile set example
Default profile set to example.
No key set for profile. Use fal profile key to set a key.

❯ fal profile key
Enter the key: invalid
Invalid key. The key must be in the format key:value.
Enter the key: 112f05b4-6ee8-4d06-bdb1-7ba38789ef8e:954285993fa8e651dac37a03ea2efbc9
Key set for profile example.
```

> [!NOTE]
>
> **Note:**
>
> The key used in the example above is no longer valid. 😉

#### Listing Profiles

To list all available profiles, use the `fal profile list` command:

```sh theme={null}
❯ fal profile list
```

| Default | Profile | Settings |
| - | - | - |
| | me | key |
| | comfy | key |
| \* | example | key |

#### Setting a Default Profile

To set a default profile, use the `fal profile set` command followed by the profile name:

```sh theme={null}
❯ fal profile set comfy
Default profile set to comfy.

❯ fal profile list
```

| Default | Profile | Settings |
| - | - | - |
| | me | key |
| \* | comfy | key |
| | example | key |

After setting the default profile, you can directly access the account information without specifying the profile name.

```sh theme={null}
❯ fal app list
```

| Name | Revision | Auth | Min Concurrency | Max Concurrency | Max Multiplexing | Keep Alive | Request Timeout | Startup Timeout | Machine Type | Runners | Regions |
| - | - | - | - | - | - | - | - | - | - | - | - |
| my-app | 11111111-2222-333… | shared | 0 | 10 | 1 | 300 | 3600 | 600 | ........ | 0 | |

#### Deleting a Profile

To delete a profile, use the `fal profile delete` command followed by the profile name:

```sh theme={null}
❯ fal profile delete example
Profile example deleted.
```

## Command Reference

```bash theme={null}
Usage: fal profile [-h] command ...

Profile management.

Commands:
  command
    list      List all profiles.
    set       Set default profile. If the profile doesn't exist, you'll be prompted to create it.
    unset     Unset default profile.
    key       Set key for profile.
    host      Set fal host.
    create    Create a new profile.
    delete    Delete profile.
```

> [!NOTE]
>
> `fal profiles` is an alias for `fal profile`.

### create

Create a new named profile and set it as the default:

```bash theme={null}
fal profile create <PROFILE>
```

If the profile already exists the command is a no-op. After creation you'll typically run `fal profile key` to attach an API key.

### unset

Clear the default profile selection:

```bash theme={null}
fal profile unset
```

After unsetting, `fal` falls back to environment variables (`FAL_KEY`, `FAL_HOST`).

### host

Override the fal API host for the active profile. Most users don't need this — it's primarily useful for self-hosted or staging deployments:

```bash theme={null}
fal profile host <HOST>
```

### list

```bash theme={null}
Usage: fal profile list [-h] [--output {pretty,json}] [--json]
```

Supports JSON output for scripting:

```bash theme={null}
fal profile list --json
```
