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

# fal runners

> Manage fal runners from the CLI. List runners by state, read their logs, and show GPU usage. Stop a runner, or open a shell on it.

```bash theme={null}
Usage: fal runners [-h] command ...

Manage fal runners.

Options:
  -h, --help  show this help message and exit

Commands:
  command
    stop        Stop a runner gracefully.
    kill        Kill a runner.
    list        List runners.
    gpus        Show GPU usage summary across runners.
    logs (log)  Show logs for a runner.
    shell       Open a shell on a runner.
    exec        Execute a command on a runner.
```

## List

```bash theme={null}
Usage: fal runners list [-h] [--team TEAM] [--since SINCE]
                        [--state {all,idle,running,pending,setup,crash_backoff,terminated} [...]]
                        [--output {pretty,json}] [--json]

List runners.

Options:
  -h, --help            show this help message and exit
  --team TEAM           The team to use.
  --since SINCE         Show terminated runners since the given time. Accepts 'now', relative like '30m', '1h', '1d', or an ISO timestamp. Max 24 hours.
  --state {all,idle,running,pending,setup,crash_backoff,terminated} [{all,idle,running,pending,setup,crash_backoff,terminated} ...]
                        Filter by runner state(s). Choose one or more, or 'all' (default).

Output:
  --output {pretty,json}
                        Modify the command output
  --json                Output in JSON format (same as --output json)
```

The runner table displays the following columns:

* **Alias** — Application name
* **Machine Type** — Hardware type (e.g. `GPU-A100`, `GPU-H100`)
* **Runner ID** — Unique runner identifier
* **In Flight Requests** — Number of active requests
* **Expires In** — Time until runner expires
* **Uptime** — How long the runner has been running
* **Revision** — Application revision
* **State** — Current runner state

## Logs

```bash theme={null}
Usage: fal runners logs [-h] [--output {pretty,json}] [--json] [--team TEAM]
                        [--search SEARCH] [--since SINCE] [--until UNTIL]
                        [--follow] [--lines LINES]
                        id

Show logs for a runner.

Positional Arguments:
  id                    Runner ID.

Options:
  -h, --help            show this help message and exit
  --team TEAM           The team to use.
  --search SEARCH       Search for string in logs.
  --since SINCE         Show logs since the given time. Accepts 'now', relative like '30m', '1h', or an ISO timestamp. Defaults to runner start time or to '1m ago' in --follow mode.
  --until UNTIL         Show logs until the given time. Accepts 'now', relative like '30m', '1h', or an ISO timestamp. Defaults to runner finish time or 'now' if it is still running.
  --follow, -f          Follow logs live. If --since is not specified, implies '--since 1m ago'.
  --lines, -n LINES     Only show latest N log lines. If '+' prefix is used, show oldest N log lines. Ignored if --follow is used.

Output:
  --output {pretty,json}
                        Modify the command output
  --json                Output in JSON format (same as --output json)
```

## Stop

```bash theme={null}
Usage: fal runners stop [-h] [--team TEAM] id

Stop a runner gracefully.

Positional Arguments:
  id           Runner ID.

Options:
  -h, --help   show this help message and exit
  --team TEAM  The team to use.
```

## Kill

```bash theme={null}
Usage: fal runners kill [-h] [--team TEAM] id

Kill a runner.

Positional Arguments:
  id           Runner ID.

Options:
  -h, --help   show this help message and exit
  --team TEAM  The team to use.
```

## Shell

```bash theme={null}
Usage: fal runners shell [-h] [--team TEAM] id

Open an interactive shell session inside a running runner.

Positional Arguments:
  id           Runner ID.

Options:
  -h, --help   show this help message and exit
  --team TEAM  The team to use.
```

### Use Cases

* **Debug running code**: Inspect the runtime environment of a live runner
* **Check dependencies**: Verify installed packages and their versions
* **Examine state**: Inspect files, environment variables, and runtime state
* **Troubleshoot issues**: Diagnose problems in production runners

### Example

Connect to a running runner:

```bash theme={null}
# Get runner ID from runners list
fal runners list

# Connect to the runner
fal runners shell runner_abc123xyz
```

Once connected, you have full shell access within the runner's container environment. Press `Ctrl+D` or type `exit` to disconnect.

## Exec

Run a one-off command on a runner without opening an interactive shell.

```bash theme={null}
Usage: fal runners exec [-h] [--team TEAM] [-it] id [command ...]

Execute a command on a runner.

Positional Arguments:
  id                  Runner ID.
  command             Command to execute (after `--`).

Options:
  -h, --help          show this help message and exit
  --team TEAM         The team to use.
  -it, --interactive  Allocate a TTY and attach stdin (interactive mode).
```

The command and its arguments are passed after a `--` separator so they aren't parsed as flags by the `fal` CLI.

### Examples

```bash theme={null}
# Print environment variables on the runner
fal runners exec runner_abc123xyz -- env

# Tail an arbitrary log file inside the container
fal runners exec runner_abc123xyz -- tail -f /var/log/my-app.log

# Run an interactive Python REPL
fal runners exec runner_abc123xyz -it -- python
```

***

## GPUs

Show a GPU usage summary across all runners in the account.

```bash theme={null}
Usage: fal runners gpus [-h] [--output {pretty,json}] [--json] [--team TEAM]

Show GPU usage summary across runners.

Options:
  -h, --help            show this help message and exit
  --team TEAM           The team to use.

Output:
  --output {pretty,json}
                        Modify the command output
  --json                Output in JSON format (same as --output json)
```

For the GPUs behind a single application rather than the whole account, see
[`fal apps gpus`](apps/gpus.md).
