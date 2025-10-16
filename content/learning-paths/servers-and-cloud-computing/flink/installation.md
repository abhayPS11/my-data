---
title: Install Apache Flink
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Apache Flink on GCP VM
This page guides you through installing **Apache Flink** and its prerequisites on a **GCP SUSE Arm64 VM**.

### 

```console
sudo zypper refresh
sudo zypper update -y
sudo zypper install -y java-17-openjdk java-17-openjdk-devel
```

### Download Apache Flink Binary

```console
cd /opt
sudo wget https://dlcdn.apache.org/flink/flink-2.1.0/flink-2.1.0-bin-scala_2.12.tgz
```

### Extract the Downloaded Archive

```
sudo tar -xvzf flink-2.1.0-bin-scala_2.12.tgz
```

**Rename the extracted directory for convenience:**

```console
sudo mv flink-2.1.0 /opt/flink
```

### Configure Environment Variables
To set up environment variables:

```console
echo "export FLINK_HOME=/opt/flink" >> ~/.bashrc
echo "export PATH=\$FLINK_HOME/bin:\$PATH" >> ~/.bashrc
```

```console
sudo mkdir -p /opt/flink/log
sudo chown -R $(whoami):$(id -gn) /opt/flink/log
sudo chmod -R 755 /opt/flink/log
```

**Apply the changes:**

```console
source ~/.bashrc
```

### Verify the Installation

Check the Flink version to confirm the installation:

```console
flink -v
```

You should see an output similar to:

```output
Version: 2.1.0, Commit ID: 4cb6bd3
```
