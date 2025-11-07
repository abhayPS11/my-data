---
title: Install TensorFlow 
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## TensorFlow Installation on GCP SUSE VM
TensorFlow is a widely used **open-source machine learning library** developed by Google, designed for building and deploying ML models efficiently. On ARM64 SUSE VMs, TensorFlow can run on CPU natively, or on GPU if available.

### System Preparation
Update the system and install Python3 and pip:

```console
sudo zypper refresh
sudo zypper update -y
sudo zypper install -y python3 python3-pip python3-venv
```

### Verify Python version:

```console
python3 --version
pip3 --version
```

### Create a Virtual Environment (Recommended)
It is recommended to install TensorFlow in a Python virtual environment:

```console
python3 -m venv tf-venv
source tf-venv/bin/activate
```
Your shell prompt should now show (`tf-venv`).

### Upgrade pip
Make sure pip is up-to-date:

```console
pip install --upgrade pip
```

### Install TensorFlow
Install the latest stable TensorFlow version for ARM64:

```console
pip install tensorflow==2.20.0
```

### Verify installation:

```console
python -c "import tensorflow as tf; print(tf.__version__)"
```

You should see an output similar to:
```output
2.20.0
```

