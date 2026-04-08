---
title: Deploy Ray on GCP SUSE ARM64
weight: 1

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Deploy Ray on GCP SUSE ARM64 (Arm)

This section guides you through installing Ray on a GCP ARM64 (Axion) virtual machine and setting up a distributed computing cluster on a single node.

You will configure the environment, install dependencies, and initialize a Ray cluster optimized for Arm-based infrastructure.

## Update your system

```console
sudo zypper refresh
sudo zypper update -y
```

## Install required dependencies

```bash
sudo zypper install -y python311 python311-devel python311-pip git curl gcc gcc-c++ make
```

## Create Python environment

```bash
python3.11 -m venv ray-env
source ray-env/bin/activate

pip install --upgrade pip setuptools wheel
```

## Install Ray and ML dependencies

```bash
pip install "ray[default]" "ray[train]" "ray[tune]" "ray[serve]"
pip install torch torchvision pandas scikit-learn
```

## Start the Ray cluster

```bash
ray stop
ray start --head --dashboard-host=0.0.0.0 --num-cpus=4
```

The output is similar to:

```output
Ray runtime started.
Local node IP: 10.x.x.x
Dashboard: 127.0.0.1:8265
```

## Verify cluster status

```bash
ray status
```

The output shows:

```output
1 node active
4 CPUs available
No failures
```

## Access the dashboard

Open in browser:

```
http://<VM-IP>:8265
```

This dashboard provides visibility into jobs, tasks, and resource utilization.

## What you've learned and what's next

You have successfully:

* Installed Ray on ARM-based SUSE VM
* Initialized a Ray cluster
* Verified cluster status and dashboard

Next, you will run distributed workloads using Ray.
