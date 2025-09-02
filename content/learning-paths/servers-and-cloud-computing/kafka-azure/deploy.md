---
title: Install Kafka on Azure Cobalt 100 processors
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Kafka on Azure Cobalt 100
This guide walks you through installing Apache **Kafka 3.7.1** on an **Azure Cobalt 100 Arm-based VM** or inside a **Docker container running Azure Linux 3.0**. You’ll download Kafka, extract it into `/opt`, configure permissions, and verify the installation by checking the installed version.

Follow the below instructions to install Kafka on Ubuntu virtual machine.

### Install Java

Kafka requires Java to run. Install it using the following commands:
```console
sudo apt update
sudo apt install -y openjdk-17-jdk
```
### Download and Install Kafka

This sequence of commands downloads `Kafka 3.7.1` to the `/opt` directory, extracts the tarball, renames the folder to kafka for simplicity, and sets ownership so the current user can access and manage the Kafka installation. It prepares the system for running Kafka without permission issues.

```console
cd /opt
sudo curl -O https://archive.apache.org/dist/kafka/3.7.1/kafka_2.13-3.7.1.tgz
sudo tar -xvzf kafka_2.13-3.7.1.tgz
sudo mv kafka_2.13-3.7.1 kafka
sudo chown -R $USER:$USER kafka
```

### Check installed Kafka version

These commands navigate to the Kafka installation directory and check the installed Kafka version, confirming that Kafka has been successfully installed and is ready for use.
```console
cd /opt/kafka
bin/kafka-topics.sh --version
```

You should see an output similar to:
```output
3.7.1
```
Kafka installation is complete. You can now proceed with the baseline testing.
