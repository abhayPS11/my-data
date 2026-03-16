---
title: Install MinIO
weight: 1
---

# Install prerequisites and MinIO Server

This section prepares the environment and installs the **MinIO object storage server** on an ARM-based Azure virtual machine.

## Azure VM requirement

This guide requires an **ARM64-based Azure virtual machine**, such as **Azure Cobalt 100**.

Recommended configuration:

| Resource | Recommendation |
|--------|----------------|
VM Size | Standard_D4ps_v6 |
Operating System | Ubuntu 22.04 |
Architecture | ARM64 |
Disk | 64 GB or larger |

Connect to your VM using SSH.

```bash
ssh azureuser@<VM_PUBLIC_IP>
```

Update system packages

Update the package list and upgrade installed packages.

sudo apt update && sudo apt upgrade -y
Install required utilities

Install required packages for downloading and running MinIO.

sudo apt install wget curl nano tree python3 python3-pip -y

Verify Python installation.

python3 --version

Example output:

Python 3.x.x
Install MinIO Server

Download the MinIO ARM64 binary.

wget https://dl.min.io/server/minio/release/linux-arm64/minio

Make the binary executable.

chmod +x minio

Move the binary to a system path.

sudo mv minio /usr/local/bin/

Verify the installation.

minio --version

Example output:

minio version RELEASE.xxxxxx
Runtime: go1.x linux/arm64

After installing MinIO, you can proceed to start the server and configure object storage.
