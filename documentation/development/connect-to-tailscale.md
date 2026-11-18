> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Connect Runners to Your Private Network

> Reach private APIs and services from your runners, and connect to runners with SSH, with Tailscale. Nothing is exposed to the internet.

Use Tailscale to connect your runners to your private network. Nothing is exposed to the internet. You can:

* **Reach private services from your app**, for example an internal API or a model store.
* **Connect to a runner with SSH**, for example to run commands, copy files with `scp`, or forward a port.

[Tailscale](https://tailscale.com/kb/1151/what-is-tailscale) is a private network service. Each runner joins your private network, called a tailnet, when it starts. Tailscale removes the runner from the tailnet some time after the runner stops.

> [!TIP]
>
> For a quick shell on a runner, use [`fal runners shell`](../deployment/debug-runners.md). You do not need Tailscale for that.

## Prerequisites

1. In the Tailscale admin console, create an [auth key](https://tailscale.com/kb/1085/auth-keys). Make it **reusable** and **ephemeral**. Add a [tag](https://tailscale.com/kb/1068/tags), for example `tag:fal-runner`.
2. Add an [access control policy](https://tailscale.com/kb/1018/acls) that lets `tag:fal-runner` reach only the services that your app needs.
3. Store the auth key as a fal [secret](manage-secrets-securely.md):

```bash theme={null}
fal secrets set TS_AUTHKEY=tskey-auth-...
```

> [!NOTE]
>
> Use a reusable key because each runner joins the tailnet with the same key. Use an ephemeral key so that Tailscale removes stopped runners automatically.

> [!WARNING]
>
> An auth key expires after 90 days or less. After the key expires, new runners cannot join the tailnet, and `setup()` fails. Create a new key and update the `TS_AUTHKEY` secret before the old key expires.

## Example App

> [!NOTE]
>
> By default, runners do not have the `CAP_NET_ADMIN` capability, and they cannot create a TUN device. For this reason, the app runs `tailscaled` in [userspace networking mode](https://tailscale.com/kb/1112/userspace-networking). In this mode, the app sends traffic to the tailnet through a local SOCKS5 or HTTP proxy.

This app installs Tailscale in a [custom container image](use-custom-container-image.md). The `setup()` method starts `tailscaled`, joins the tailnet, and waits for the connection. The endpoint then calls a private service through the SOCKS5 proxy.

```python theme={null}
import os
import subprocess
import time

import fal
from fal.container import ContainerImage
from pydantic import BaseModel

dockerfile_str = """
FROM python:3.11-slim
RUN apt-get update && apt-get install -y curl ca-certificates && rm -rf /var/lib/apt/lists/*
RUN curl -fsSL https://tailscale.com/install.sh | sh
RUN pip install --no-cache-dir "httpx[socks]"
"""

TAILSCALE_PROXY = "socks5h://localhost:1055"


class Input(BaseModel):
    path: str = "/health"


class Output(BaseModel):
    status_code: int
    body: str


class TailscaleApp(fal.App):
    machine_type = "S"
    image = ContainerImage.from_dockerfile_str(dockerfile_str)
    secrets = ["TS_AUTHKEY"]

    def setup(self):
        self.tailscaled = subprocess.Popen(
            [
                "tailscaled",
                "--tun=userspace-networking",
                "--state=mem:",
                "--statedir=/tmp/tailscale",
                "--socket=/tmp/tailscaled.sock",
                "--socks5-server=localhost:1055",
                "--outbound-http-proxy-listen=localhost:1056",
            ]
        )
        subprocess.run(
            [
                "tailscale",
                "--socket=/tmp/tailscaled.sock",
                "up",
                f"--auth-key={os.environ['TS_AUTHKEY']}",
                f"--hostname=fal-{os.environ['FAL_JOB_ID']}",
                "--advertise-tags=tag:fal-runner",
                "--timeout=60s",
            ],
            check=True,
        )

    @fal.endpoint("/")
    def call_private_service(self, input: Input) -> Output:
        import httpx

        with httpx.Client(proxy=TAILSCALE_PROXY) as client:
            response = client.get(f"http://my-internal-api{input.path}")
        return Output(status_code=response.status_code, body=response.text)

    def teardown(self):
        subprocess.run(
            ["tailscale", "--socket=/tmp/tailscaled.sock", "logout"],
            check=False,
        )
        self.tailscaled.terminate()
```

Replace `my-internal-api` with the [MagicDNS](https://tailscale.com/kb/1081/magicdns) name or Tailscale IP address of your service.

## Send Traffic Through the Tailnet

In userspace networking mode, only traffic that goes through the proxy reaches the tailnet. Other traffic uses the normal runner network. Use one of these methods:

* **SOCKS5 proxy** on `localhost:1055`. Use this proxy for clients that support SOCKS5.
* **HTTP proxy** on `localhost:1056`. Use this proxy for HTTP clients.

Set the proxy on each client that must reach the tailnet. Do not set it for other traffic.

> [!WARNING]
>
> Use the `socks5h://` scheme, not `socks5://`. With `socks5://`, some clients such as `requests` look up the host name on the runner. The runner cannot resolve MagicDNS names, so the request fails.

## Connect to a Runner with SSH

You can use the tailnet to open an SSH session on a runner. The session runs inside the runner, so you see the same files and processes as your app. In userspace networking mode, `tailscaled` sends incoming tailnet connections to services on `localhost`. You do not need to open a public port.

### Use Tailscale SSH

[Tailscale SSH](https://tailscale.com/kb/1193/tailscale-ssh) uses your tailnet identity, so you do not manage SSH keys. Tailscale SSH keeps its host keys in the `--statedir` directory of `tailscaled`, as in the example app. Add `--ssh` to the `tailscale up` command in `setup()`:

```python theme={null}
subprocess.run(
    [
        "tailscale",
        "--socket=/tmp/tailscaled.sock",
        "up",
        f"--auth-key={os.environ['TS_AUTHKEY']}",
        f"--hostname=fal-{os.environ['FAL_JOB_ID']}",
        "--advertise-tags=tag:fal-runner",
        "--ssh",
        "--timeout=60s",
    ],
    check=True,
)
```

Then add an SSH rule to your tailnet policy. This rule lets members of your tailnet connect to runners as `root`:

```json theme={null}
"ssh": [
  {
    "action": "accept",
    "src": ["autogroup:member"],
    "dst": ["tag:fal-runner"],
    "users": ["root"]
  }
]
```

From a device on your tailnet, connect with the hostname of the runner. Find the hostname on the **Machines** page of the admin console, or run `tailscale status`.

```bash theme={null}
ssh root@fal-<job-id>
```

### Use OpenSSH

If you want to use SSH keys, run `sshd` in the runner. Install `openssh-server` in your container image. Then start `sshd` in `setup()` before `tailscale up`:

```python theme={null}
os.makedirs("/run/sshd", exist_ok=True)
os.makedirs("/root/.ssh", mode=0o700, exist_ok=True)
with open("/root/.ssh/authorized_keys", "w") as f:
    f.write(os.environ["SSH_PUBLIC_KEY"] + "\n")
subprocess.run(["ssh-keygen", "-A"], check=True)
self.sshd = subprocess.Popen(
    [
        "/usr/sbin/sshd",
        "-D",
        "-o", "ListenAddress=127.0.0.1",
        "-o", "PasswordAuthentication=no",
    ]
)
```

Store your public key as a fal secret named `SSH_PUBLIC_KEY`, and add it to the `secrets` list of the app. `sshd` listens only on `localhost`. Only devices on your tailnet can connect to it.

> [!NOTE]
>
> If the SSH client also runs `tailscaled` in userspace networking mode, for example on another runner, add `-o ProxyCommand="tailscale --socket=/tmp/tailscaled.sock nc %h %p"` to the `ssh` command.

## Troubleshooting

* **`setup()` fails with `KeyError: 'TS_AUTHKEY'`.** Make sure that the `TS_AUTHKEY` secret exists in the app [environment](../deployment/manage-environments.md).
* **`tailscale up` fails with `invalid key`.** The auth key expired, was revoked, or was copied incorrectly. Create a new key and set the secret again.
* **The runner closes the SSH connection before login.** Make sure that `tailscaled` starts with `--statedir`. Without it, Tailscale SSH has no host keys and does not start.
* **The runner refuses the SSH connection.** Make sure that your tailnet policy has an SSH rule that lets you connect to `tag:fal-runner`.
* **The runner joins but cannot reach the service.** Make sure that your access control policy lets `tag:fal-runner` reach the service and port.
* **Connections are slow.** If a direct connection is not possible, Tailscale sends traffic through a [DERP relay](https://tailscale.com/kb/1232/derp-servers). Run `tailscale --socket=/tmp/tailscaled.sock status` in `setup()` and log the output to see the connection type.
* **Old nodes stay in the admin console.** Use an ephemeral auth key. Tailscale removes ephemeral nodes some time after they go offline. This can take 30 minutes or more.
