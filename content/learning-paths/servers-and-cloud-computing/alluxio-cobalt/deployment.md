---
title: Deploy Alluxio on Azure Cobalt 100
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Deploy Alluxio on Azure Cobalt 100 (Arm)

This section guides you through installing Alluxio on an Azure Cobalt 100 Arm-based virtual machine and configuring it with local storage.

You will set up a unified data orchestration layer that sits between compute frameworks and storage systems.

## Update your system

```bash
sudo apt update && sudo apt upgrade -y
```

## Install required dependencies

```bash
sudo apt install -y wget curl tar rsync nano
```

## Install Java 11 (Required)

Alluxio supports Java 8 and 11. Java 17 is not supported.

```bash
wget -qO - https://packages.adoptium.net/artifactory/api/gpg/key/public | \
sudo gpg --dearmor -o /usr/share/keyrings/adoptium.gpg

echo "deb [signed-by=/usr/share/keyrings/adoptium.gpg] https://packages.adoptium.net/artifactory/deb noble main" | \
sudo tee /etc/apt/sources.list.d/adoptium.list

sudo apt update
sudo apt install -y temurin-11-jdk
```

**Set Java:**

```bash
sudo update-alternatives --config java
```

**Verify:**

```bash
java -version
```

## Download and install Alluxio

```bash
cd /opt
sudo wget https://downloads.alluxio.io/downloads/files/2.9.4/alluxio-2.9.4-bin.tar.gz
sudo tar -xvzf alluxio-2.9.4-bin.tar.gz
sudo mv alluxio-2.9.4 alluxio
sudo chown -R $USER:$USER /opt/alluxio
```

## Configure environment variables

```bash
echo 'export ALLUXIO_HOME=/opt/alluxio' >> ~/.bashrc
echo 'export PATH=$PATH:$ALLUXIO_HOME/bin' >> ~/.bashrc
source ~/.bashrc
```

## Configure Alluxio

```bash
cd /opt/alluxio/conf
cp alluxio-env.sh.template alluxio-env.sh
cp alluxio-site.properties.template alluxio-site.properties
```

## Configure RAM-based storage

**Edit:**

```bash
nano alluxio-env.sh
```

**Add:**

```bash
export ALLUXIO_RAM_FOLDER=/dev/shm
```

## Configure core properties

```bash
nano alluxio-site.properties
```

```bash
alluxio.master.hostname=localhost
alluxio.worker.memory.size=6GB
alluxio.master.mount.table.root.ufs=/mnt/data
```

## Setup storage directory

```bash
sudo mkdir -p /mnt/data
sudo chmod -R 777 /mnt/data
```

## Start Alluxio

```bash
alluxio format
alluxio-start.sh local NoMount
```

## Verify Alluxio services

```bash
jps
```

**Expected output:**

```output
AlluxioMaster
AlluxioWorker
AlluxioProxy
Access Alluxio Web UI
```

**Open:**

```text
http://<VM-IP>:19999
```

## What you've learned and what's next

You have successfully:

- Installed Alluxio on an Arm-based VM
- Configured compute and storage layers
- Enabled memory-based data caching
- Verified cluster health via UI

You are now ready to integrate Alluxio with analytics frameworks.
