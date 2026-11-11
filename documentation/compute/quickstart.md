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

# Quickstart with Compute

> Get up and running with fal Compute in minutes. This guide will walk you through provisioning your first GPU instance and connecting to it.

> [!WARNING]
>
> **Enterprise**
>
> To request access and learn more about fal Compute, please visit the [dashboard](https://fal.ai/dashboard) to get started.

## Prerequisites

Before you begin, make sure you have:

* A fal.ai account with Compute access
* An SSH key pair for secure instance access
* Basic familiarity with SSH and command line tools

### Generate SSH Key (if needed)

If you don’t have an SSH key pair, generate one:

```bash theme={null}
# Generate a new SSH key pair
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# Display your public key (you'll need this for instance creation)
cat ~/.ssh/id_rsa.pub
```

## Step 1: Create Your Instance

**1. Access the Dashboard**

* Navigate to the [fal Compute Dashboard](https://fal.ai/dashboard/compute)
* Click the **“Create”** button

**2. Configure Your Instance**

* **Instance Type**: Choose between:
  * `1xH100-SXM`: Single GPU for development and smaller workloads
  * `8xH100-SXM`: Eight GPUs for large-scale training and inference
* **Sector Selection**:
  * **Default**: For single-instance workloads
  * **Specific Sector**: For multi-node clusters with InfiniBand connectivity
* **SSH Key**: Paste your public SSH key for secure access

**3. Launch Instance**

* Review your configuration
* Click **“Create”** to provision your instance
* Wait for the instance to reach “ready” state (typically 2-3 minutes)

## Step 2: Connect to Your Instance

Once your instance is running, you’ll receive connection details:

```bash theme={null}
# Connect via SSH (replace with your actual connection details)
ssh ubuntu@your-instance-ip

# Example connection
ssh ubuntu@203.0.113.10
```

## Step 3: Verify Your Setup

After connecting, check your GPU resources:

```bash theme={null}
# Check GPU availability
nvidia-smi

# Verify CUDA installation
nvcc --version

# Check storage
df -h

# View system resources
htop
```

Expected output for 1xH100-SXM:

```bash theme={null}
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 525.xx.xx    Driver Version: 525.xx.xx    CUDA Version: 12.x   |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA H100-SXM...  Off  | 00000000:01:00.0 Off |                    0 |
| N/A   27C    P0    68W / 700W |      0MiB / 81920MiB |      0%      Default |
|                               |                      |             Disabled |
+-------------------------------+----------------------+----------------------+
```

## Step 4: Install Your Dependencies

Install your required software stack:

```bash theme={null}
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install Python and pip (if not already installed)
sudo apt install python3 python3-pip -y

# Install common ML libraries
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121

# Verify PyTorch can see your GPU
python3 -c "import torch; print(f'CUDA available: {torch.cuda.is_available()}'); print(f'GPU count: {torch.cuda.device_count()}')"
```

## Step 5: Run Your First Workload

Test your setup with a simple GPU workload:

```py test_gpu.py icon="python"  theme={null}
import torch
import time

# Check if CUDA is available
print(f"CUDA available: {torch.cuda.is_available()}")
print(f"GPU count: {torch.cuda.device_count()}")

if torch.cuda.is_available():
    # Create large tensors on GPU
    device = torch.device('cuda')

    # Simple matrix multiplication test
    print("Running GPU compute test...")
    start_time = time.time()

    a = torch.randn(10000, 10000, device=device)
    b = torch.randn(10000, 10000, device=device)
    c = torch.matmul(a, b)

    end_time = time.time()
    print(f"Matrix multiplication completed in {end_time - start_time:.2f} seconds")
    print(f"GPU memory used: {torch.cuda.memory_allocated(device) / 1024**3:.2f} GB")
```

Run the test:

```bash theme={null}
python3 test_gpu.py
```

## Step 6: Transfer Your Data

For training workloads, you’ll need to transfer your datasets:

```bash theme={null}
# Using scp to transfer files
scp -r /local/path/to/dataset user@your-instance-ip:/remote/path/

# Using rsync for large datasets
rsync -avz -P /local/path/to/dataset/ user@your-instance-ip:/remote/path/dataset/

# Or download directly on the instance
wget https://example.com/dataset.tar.gz
tar -xzf dataset.tar.gz
```

## Next Steps

Now that your instance is running, you can:

### For Machine Learning

* **Training**: Start your training scripts with dedicated GPU resources
* **Fine-tuning**: Adapt pre-trained models with your custom datasets
* **Inference**: Deploy models for batch or real-time inference

### For Multi-GPU Workloads (8xH100)

* **Distributed Training**: Use frameworks like DeepSpeed, Horovod, or PyTorch DDP
* **Model Parallelism**: Split large models across multiple GPUs
* **Data Parallelism**: Process multiple batches simultaneously

### For Multi-Node Clusters

* **InfiniBand Setup**: Configure high-speed inter-node communication
* **Cluster Management**: Use tools like SLURM or Kubernetes for job scheduling
* **Distributed Computing**: Scale workloads across multiple instances

## Managing Your Instance

```bash theme={null}
# Monitor GPU usage
watch -n 1 nvidia-smi

# Check disk usage
df -h

# Monitor system resources
htop

# Check network connectivity (for multi-node)
ibstatus  # InfiniBand status
```

## Troubleshooting

### Common Issues

**SSH Connection Failed**

* Verify your SSH key is correctly configured
* Check instance status in the dashboard
* Ensure your IP is not blocked by firewalls

**GPU Not Detected**

* Run `nvidia-smi` to check GPU status
* Verify CUDA installation with `nvcc --version`
* Restart the instance if GPU drivers aren’t loaded

**Out of Memory Errors**

* Monitor GPU memory with `nvidia-smi`
* Reduce batch sizes in your training scripts
* Use gradient checkpointing to save memory

### Getting Help

* Check the [fal.ai documentation](https://docs.fal.ai) for detailed guides
* Contact support through the dashboard for technical issues
* Join the community forums for user discussions
