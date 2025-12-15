---
title: Install RabbitMQ
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install RabbitMQ on Azure Cobalt 100
This guide covers installing RabbitMQ 4.2.0 on an  Ubuntu Pro 24.04 Arm virtual machine, including the required Erlang setup and management plugin.

Follow the instructions below to install RabbitMQ on the Ubuntu Pro 24.04 virtual machine.

### Update system and install build dependencies

```console
sudo apt update
sudo apt install -y build-essential libssl-dev libncurses-dev libtinfo-dev \
                    libgl1-mesa-dev libglu1-mesa-dev libpng-dev libssh-dev \
                    unixodbc-dev wget tar xz-utils git
```

### Build and install Erlang OTP 26


```console
# Clone Erlang source
git clone https://github.com/erlang/otp.git
cd otp

# Checkout OTP 26 branch
git checkout OTP-26

# Clean previous builds
make clean

# Configure build with SSL/crypto support
./configure --prefix=/usr/local/erlang-26 \
            --enable-smp-support \
            --enable-threads \
            --enable-kernel-poll \
            --with-ssl

# Build and install
make -j$(nproc)
sudo make install
```

### Download and install RabbitMQ

```console
cd ~
wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v4.2.0/rabbitmq-server-generic-unix-4.2.0.tar.xz
sudo mkdir -p /opt/rabbitmq
sudo tar -xvf rabbitmq-server-generic-unix-4.2.0.tar.xz -C /opt/rabbitmq --strip-components=1

# Create directories for logs and database
sudo mkdir -p /var/lib/rabbitmq /var/log/rabbitmq
sudo chown -R $USER:$USER /var/lib/rabbitmq /var/log/rabbitmq
```

#### Update PATH environment variable

```console
export PATH=/usr/local/erlang-26/bin:/opt/rabbitmq/sbin:$PATH
```

Add this line to `~/.bashrc` or `~/.profile` for persistence.

### Configure RabbitMQ systemd service

Create `/etc/systemd/system/rabbitmq.service`:

```ini
[Unit]
Description=RabbitMQ broker
After=network.target

[Service]
Type=simple
User=azureuser
Group=azureuser

Environment=HOME=/home/azureuser
Environment=RABBITMQ_HOME=/opt/rabbitmq
Environment=RABBITMQ_MNESIA_BASE=/var/lib/rabbitmq
Environment=RABBITMQ_LOG_BASE=/var/log/rabbitmq
Environment=PATH=/usr/local/erlang-26/bin:/opt/rabbitmq/sbin:/usr/bin

ExecStart=/opt/rabbitmq/sbin/rabbitmq-server
ExecStop=/opt/rabbitmq/sbin/rabbitmqctl shutdown

Restart=on-failure
RestartSec=10
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

Reload systemd and start RabbitMQ:

```console
sudo systemctl daemon-reload
sudo systemctl enable rabbitmq
sudo systemctl start rabbitmq
sudo systemctl status rabbitmq
```

### Enable RabbitMQ management plugin

```console
# Ensure config directory exists
sudo mkdir -p /opt/rabbitmq/etc/rabbitmq
sudo chown -R $USER:$USER /opt/rabbitmq/etc/rabbitmq

# Enable management plugin
rabbitmq-plugins enable rabbitmq_management
```

### Verify installation

**Erlang version:**

```console
/usr/local/erlang-26/bin/erl erl -eval 'io:format("~s~n", [erlang:system_info(system_version)]), halt().' -noshell
```

You should see an output similar to:
```output
Erlang/OTP 26 [erts-14.2.5.12] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1] [jit]
```

**RabbitMQ version:**

```console
rabbitmqctl version
```

You should see an output similar to:
```output
4.2.0
```
