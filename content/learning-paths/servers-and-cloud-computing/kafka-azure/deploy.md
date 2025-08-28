---
title: Install Kafka on Azure Cobalt 100 processors
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Kafka on Azure Cobalt 100
This guide walks you through installing Apache **Kafka 3.7.1** on an **Azure Cobalt 100 Arm-based VM** or inside a **Docker container running Azure Linux 3.0**. You’ll download Kafka, extract it into `/opt`, configure permissions, and verify the installation by checking the installed version.

Within your running docker container image or your custom Azure Linux VM, follow the instructions to install Kafka.

## Install Kafka

1. Navigate to /opt directory

```console
cd /opt
```

2. Download Kafka 3.7.1

```console
sudo curl -O https://archive.apache.org/dist/kafka/3.7.1/kafka_2.13-3.7.1.tgz
```

3. Extract the tarball

```console
sudo tar -xvzf kafka_2.13-3.7.1.tgz
```

4. Rename folder for simplicity

```console
sudo mv kafka_2.13-3.7.1 kafka
```

5. Set ownership to current user

```console
sudo chown -R $USER:$USER kafka
```

6. Check installed Kafka version

```console
cd /opt/kafka
bin/kafka-topics.sh --version
```

```output
3.7.1
```
