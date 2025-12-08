---
title: Install RabbitMQ
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install RabbitMQ on GCP SUSE Arm64 VM
This guide describes a **step-by-step installation of RabbitMQ** on a **Google Cloud Platform SUSE Linux Arm64 virtual machine**, using **RPM packages** for both **Erlang** and **RabbitMQ Server**.

RabbitMQ requires Erlang to be installed before setting up the server.


### Prerequisites

- GCP SUSE Linux Enterprise Server (Arm64)
- Root or sudo privileges
- Outbound internet access

### Refresh System Repositories
Update system metadata to ensure the latest package information is available.

```console
sudo zypper refresh
```

### Install Required System Utilities
You can install the basic tools needed to download and manage packages.

```console
sudo zypper install -y curl wget gnupg tar socat logrotate
```

### Download Erlang RPM (Arm64)
RabbitMQ depends on Erlang. Download the Erlang RPM compatible with the Arm64 architecture.

```console
wget https://github.com/rabbitmq/erlang-rpm/releases/download/v26.2.5/erlang-26.2.5-1.el8.aarch64.rpm
sudo rpm -Uvh erlang-26.2.5-1.el8.aarch64.rpm
```

### Verify Erlang Installation
Confirm that Erlang is installed correctly.

```console
erl -eval 'io:format("~s~n", [erlang:system_info(system_version)]), halt().' -noshell
```

### Download RabbitMQ Server RPM
Download the RabbitMQ Server RPM package.

```console
wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v4.2.0/rabbitmq-server-4.2.0-1.el8.noarch.rpm
sudo rpm -Uvh rabbitmq-server-4.2.0-1.el8.noarch.rpm
```

### Enable and Start RabbitMQ Service
Enable RabbitMQ to start automatically on boot and start the service immediately.

```console
sudo systemctl enable rabbitmq-server --now
```

### Verify RabbitMQ Service Status
Check the status of the RabbitMQ service.

```console
sudo systemctl status rabbitmq-server
```

The service should be in an active (running) state.

### Enable RabbitMQ Management Plugin
Enable the RabbitMQ management plugin to access the web-based dashboard.

```console
sudo rabbitmq-plugins enable rabbitmq_management
```

### Restart RabbitMQ
Restart RabbitMQ to apply plugin changes.

```console
sudo systemctl restart rabbitmq-server
```

### Verify RabbitMQ Version
Confirm the installed RabbitMQ version.

```console
sudo rabbitmqctl version
```
