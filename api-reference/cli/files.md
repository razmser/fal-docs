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

# fal files

> Manage files in fal storage from the CLI. List, upload, and download files, upload from a URL, and move or remove remote files and directories with fal files.

```bash theme={null}
fal files [-h] [--debug] [--pdb] [--cprofile] command ...

Manage fal files.

Options:
  -h, --help  show this help message and exit
Commands:
  command
    list (ls)
              List files.
    download  Download files.
    upload    Upload files.
    upload-url
              Upload file from URL.
    mv        Move or rename a remote file or directory.
    rm        Recursively remove a remote file or directory.
```

## List

```bash theme={null}
fal files list [-h] [--debug] [--pdb] [--cprofile] [--team TEAM] [path]

List files.

Positional Arguments:
  path         The path to list

Options:
  -h, --help   show this help message and exit
  --team TEAM  The team to use.

```

## Download

```bash theme={null}
fal files download [-h] [--debug] [--pdb] [--cprofile] [--team TEAM]
                          remote_path local_path

Download files.

Positional Arguments:
  remote_path  Remote path to download
  local_path   Local path to download to

Options:
  -h, --help   show this help message and exit
  --team TEAM  The team to use.
```

## Upload

```bash theme={null}
fal files upload [-h] [--debug] [--pdb] [--cprofile] [--team TEAM]
                        local_path remote_path

Upload files.

Positional Arguments:
  local_path   Local path to upload
  remote_path  Remote path to upload to

Options:
  -h, --help   show this help message and exit
  --team TEAM  The team to use.

```

## Upload URL

```bash theme={null}
fal files upload-url [-h] [--debug] [--pdb] [--cprofile] [--team TEAM]
                            url remote_path

Upload file from URL.

Positional Arguments:
  url          URL to upload
  remote_path  Remote path to upload to

Options:
  -h, --help   show this help message and exit
  --team TEAM  The team to use.
```

## Move

```bash theme={null}
fal files mv [-h] [--team TEAM] source destination

Move or rename a remote file or directory.

Positional Arguments:
  source       Remote source path
  destination  Remote destination path

Options:
  -h, --help   show this help message and exit
  --team TEAM  The team to use.
```

## Remove

```bash theme={null}
fal files rm [-h] [--team TEAM] path

Recursively remove a remote file or directory.

Positional Arguments:
  path         Remote path

Options:
  -h, --help   show this help message and exit
  --team TEAM  The team to use.
```
