---
title: Apache Arrow Environment and MinIO Setup on Arm64
weight: 1

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Apache Arrow Environment and MinIO Setup

In this section, you prepare an **Arm64-based SUSE Linux Enterprise Server (SLES) virtual machine **and install the core components required for high-performance analytics using **Apache Arrow**. You will also deploy **MinIO**, an **S3-compatible object storage service**, which will be used to store analytical datasets in later sections.

This foundation ensures all analytics libraries are natively optimized for Arm64 (Axion).

## Architecture (What You Are Building)

```text
SUSE Linux Enterprise Server (Arm64)
        |
        v
Python 3.11 Virtual Environment
        |
        v
Apache Arrow Libraries
        |
        v
MinIO (S3-Compatible Object Storage)
```

## Install System Dependencies (SUSE)

Install Python, build tools, and system libraries required by Apache Arrow and its ecosystem.

```bash
sudo zypper refresh \
sudo zypper install -y \
  python311 python311-devel python311-pip \
  gcc gcc-c++ make \
  libopenssl-devel \
  libuuid-devel \
  curl git
```

### Verify Python installation:

```bash
python3.11 --version
```

The output is similar to:
```output
Python 3.11.10
```

## Create Python Virtual Environment

Create an isolated Python environment for Arrow and analytics libraries.

```bash
python3.11 -m venv arrow-venv
source arrow-venv/bin/activate
```

### Upgrade core packaging tools:

```bash
pip install --upgrade pip setuptools wheel
```

**Why this matters:**

- Avoids conflicts with the system Python
- Ensures reproducible analytics environments
- Recommended for production-grade data workloads

## Install Apache Arrow and Required Libraries

Install Apache Arrow and supporting analytics libraries.

```bash
pip install \
  pyarrow \
  pandas \
  numpy \
  s3fs \
  grpcio \
  grpcio-tools \
  fastparquet \
  pyorc
```

### Verify Arrow installation:

```bash
python - <<EOF
import pyarrow as pa
print(pa.__version__)
EOF
```

The output is similar to:
```output
> import pyarrow as pa
> print(pa.__version__)
> EOF
23.0.1
```

This confirms Apache Arrow is correctly installed on Arm64.

## Install and Start MinIO (S3-Compatible Storage)

Download MinIO (Arm64)

```bash
curl -LO https://dl.min.io/server/minio/release/linux-arm64/minio
chmod +x minio
sudo mv minio /usr/local/bin/
```

## Start MinIO Server

```bash
mkdir -p ~/minio-data

export MINIO_ROOT_USER=minioadmin
export MINIO_ROOT_PASSWORD=minioadmin

minio server ~/minio-data --console-address :9001
```

**MinIO endpoints:**

- **S3 API:** Port 9000
- **Web Console:** Port 9001

Leave this process running.

The output is similar to:
```output
MinIO Object Storage Server
Copyright: 2015-2026 MinIO, Inc.
License: GNU AGPLv3 - https://www.gnu.org/licenses/agpl-3.0.html
Version: RELEASE.2025-09-07T16-13-09Z (go1.24.6 linux/arm64)

API: http://10.128.0.51:9000  http://127.0.0.1:9000
   RootUser: minioadmin
   RootPass: minioadmin

WebUI: http://10.128.0.51:9001 http://127.0.0.1:9001
   RootUser: minioadmin
   RootPass: minioadmin

CLI: https://docs.min.io/community/minio-object-store/reference/minio-mc.html#quickstart
   $ mc alias set 'myminio' 'http://10.128.0.51:9000' 'minioadmin' 'minioadmin'

Docs: https://docs.min.io
WARN: Detected default credentials 'minioadmin:minioadmin', we recommend that you change these values with 'MINIO_ROOT_USER' and 'MINIO_ROOT_PASSWORD' environment variables
```

## Create MinIO Bucket

Open the MinIO console in a browser:

```bash
http://<VM-IP>:9001
```

**Login credentials:**

- **Username**: minioadmin
- **Password**: minioadmin

### Create a bucket named:

```bash
arrow-data
```

### Configure S3 Credentials for Python

Export S3 credentials so Python libraries can access MinIO.

```bash
export AWS_ACCESS_KEY_ID=minioadmin
export AWS_SECRET_ACCESS_KEY=minioadmin
export AWS_DEFAULT_REGION=us-east-1
```

**Verify:**

```
env | grep AWS
```

The output is similar to:
```output
AWS_SECRET_ACCESS_KEY=minioadmin
AWS_DEFAULT_REGION=us-east-1
AWS_ACCESS_KEY_ID=minioadmin
```

## What You Have Accomplished

- Prepared a SUSE Arm64 analytics environment
- Installed Apache Arrow and dependencies
- Deployed MinIO as S3-compatible object storage
- Configured secure access for analytics workloads

## What’s Next

In the next section, you will use Apache Arrow to write and read Parquet and ORC datasets from MinIO using vectorized analytics APIs.
