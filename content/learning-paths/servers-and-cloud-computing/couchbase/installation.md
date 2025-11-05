---
title: Install Couchbase
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Couchbase on GCP VM

### System Preparation
Update and install required dependencies:

```console
sudo zypper refresh
sudo zypper update -y
sudo zypper install -y curl wget net-tools lsb-release
```
### Download Couchbase Server

```console
cd ~
wget -O couchbase-server-8.0.0-linux.aarch64.rpm \
https://packages.couchbase.com/releases/8.0.0/couchbase-server-community-8.0.0-linux.aarch64.rpm
```
**Verify the downloaded file:**

```console
ls -lh couchbase-server-8.0.0-linux.aarch64.rpm
```

### Install Couchbase Server

```console
sudo rpm -ivh couchbase-server-8.0.0-linux.aarch64.rpm
```

**Check if it is installed successfully:**

```console
rpm -qa | grep couchbase
```

### Start Couchbase Service

```console
sudo systemctl start couchbase-server
sudo systemctl enable couchbase-server
```

**Verify service status:**

```console
sudo systemctl status couchbase-server
```
