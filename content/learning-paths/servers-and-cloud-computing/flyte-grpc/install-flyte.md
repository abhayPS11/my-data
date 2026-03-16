---
title: Install Flyte and Dependencies
weight: 2

### FIXED, DO NOT MODIFY
layout: learningpathall
---

# Install Flyte and Dependencies

In this section, you prepare a SUSE Linux Enterprise Server (SLES) arm64 virtual machine and install the core components required to build machine learning workflow pipelines using Flyte.

Flyte enables scalable workflow orchestration for machine learning pipelines, while gRPC enables efficient communication between distributed services.

## Architecture overview

```text
SUSE Linux Enterprise Server (arm64)
        │
        ▼
Python 3.11 Environment
        │
        ▼
Flyte SDK
        │
        ▼
gRPC Communication Libraries
```

## Update the system

Update the system packages.

```bash
sudo zypper refresh
sudo zypper update -y
```

## Install system dependencies

Install Python and development tools.

```bash
sudo zypper install -y \
python311 python311-devel python311-pip \
gcc gcc-c++ make \
git curl
```

**Verify Python installation:**

```bash
python3.11 --version
```

The output is similar to:

```output
Python 3.11.x
```

## Install Flyte SDK

Install Flyte workflow libraries.

```bash
python3.11 -m pip install --upgrade pip
python3.11 -m pip install flytekit
```

**Verify installation:**

```bash
python3.11 -c "import flytekit; print(flytekit.__version__)"
```

## Install gRPC libraries

Install libraries used for service communication.

```bash
python3.11 -m pip install grpcio grpcio-tools protobuf
```

## Install Flyte CLI

Download the Flyte command line tool.

```bash
curl -L https://github.com/flyteorg/flytectl/releases/latest/download/flytectl_Linux_arm64.tar.gz -o flytectl.tar.gz
tar -xzf flytectl.tar.gz
sudo mv flytectl /usr/local/bin/
```

**Verify installation:**

```bash
flytectl version
```

## What you've learned and what's next

In this section, you learned how to:

- Prepare a SUSE arm64 environment
- Install Flyte SDK
- Install gRPC communication libraries
- configure Flyte CLI tools

In the next section, you will create a gRPC-based feature engineering service that will be used by the ML workflow pipeline.
