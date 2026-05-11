---
title: Setup PyTorch and DeepSpeed on GCP Axion (Arm)
weight: 1

### FIXED, DO NOT MODIFY
layout: learningpathall
---

# Setup PyTorch and DeepSpeed on GCP Axion (Arm)

This section guides you through setting up a Python AI/ML environment on a Google Cloud Axion Arm64 VM using SUSE Linux Enterprise Server.

## Learning Objectives

- Verify Arm64 environment
- Configure Python 3.11
- Create Python virtual environment
- Install PyTorch on Arm
- Install DeepSpeed in compatibility mode
- Validate AI/ML environment setup


## Verify ARM64 architecture

Run:

```bash
uname -m
```

Expected output:

```text
aarch64
```

Check CPU details:

```bash
lscpu
```


## Verify Python 3.11

Run:

```bash
python3.11 --version
```

Expected output:

```text
Python 3.11.x
```

If Python 3.11 is not installed:

```bash
sudo zypper install -y python311 python311-pip python311-devel
```

## Create Python virtual environment

```bash
python3.11 -m venv deepspeed-env
```

Activate environment:

```bash
source ~/deepspeed-env/bin/activate
```

Verify:

```bash
python --version
```


## Upgrade Python tools

```bash
pip install --upgrade pip setuptools wheel
```

## Install Ninja

Install Ninja using pip instead of zypper.

```bash
pip install ninja
```

Verify:

```bash
ninja --version
```

## Install CPU-only PyTorch

```bash
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu
```

## Verify PyTorch installation

```bash
python -c "import torch; print(torch.__version__)"
```

Check CUDA availability:

```bash
python -c "import torch; print(torch.cuda.is_available())"
```

Expected output:

```text
False
```

This is expected because GCP Axion VMs are CPU-only systems.


## Install DeepSpeed

DeepSpeed distributed CPU extensions require newer GCC versions.

Since default SUSE Arm64 images use GCC 7.x, install DeepSpeed without native extension compilation.

Export environment variables:

```bash
export DS_BUILD_OPS=0
export DS_BUILD_SHM_COMM=0
export DS_BUILD_CPU_ADAM=0
export DS_BUILD_AIO=0
```

Install DeepSpeed:

```bash
DS_BUILD_OPS=0 pip install deepspeed
```


## Verify DeepSpeed installation

```bash
ds_report
```

You may see:

```text
[NO] ....... [OKAY]
```

This is expected on CPU-only Arm64 environments.


## Create project directory

```bash
mkdir ~/deepspeed-demo

cd ~/deepspeed-demo
```


## Environment validation

Save environment details:

```bash
python --version

pip list

gcc --version
```


## Important note

Do NOT run:

```bash
deepspeed train.py
```

on this VM because DeepSpeed attempts to compile native CPU communication extensions which require GCC 9 or later.


## Troubleshooting

### SUSE repository refresh issue

You may encounter:

```text
Receive: script died unexpectedly
```

If this occurs:

- Continue if Python 3.11 is already installed
- Install Python packages using `pip`
- Avoid dependency on SUSE development repositories


## What you've learned

- Verified Arm64 environment
- Installed Python 3.11
- Created Python virtual environment
- Installed PyTorch on Arm
- Installed DeepSpeed compatibility mode
- Prepared AI/ML environment on GCP Axion


## Next

You will:

- Build AI training workloads
- Run neural network training
- Benchmark Arm64 AI workloads
- Validate CPU training performance
