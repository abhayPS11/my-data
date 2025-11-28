---
title: Install ClickHouse
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install ClickHouse on GCP VM

### Install required system packages
Ensure the system has the basic utilities needed for installation.

```console
sudo zypper refresh
sudo zypper install -y curl tar gzip sudo
```

### Download ClickHouse using the official installer
ClickHouse provides a unified installer that works across architectures, including ARM64.

```console
curl https://clickhouse.com/ | sh
```

This command downloads a single ClickHouse binary in the current directory.

### Install ClickHouse components
Run the installer with root privileges:

```console
sudo ./clickhouse install
```

This installs:

ClickHouse Server
ClickHouse Client
ClickHouse Local

Default configuration files under /etc/clickhouse-server

### Verify the installed version
Confirm that ClickHouse components are correctly installed.

```console
clickhouse --version
clickhouse server --version
clickhouse client --version
clickhouse local --version
```

### Create ClickHouse user and directories
Create the system user (if it does not already exist):

```console
sudo useradd -r -s /sbin/nologin clickhouse || true
sudo mkdir -p /var/lib/clickhouse
sudo mkdir -p /var/log/clickhouse-server
sudo mkdir -p /var/run/clickhouse-server
```

Set ownership:

```console
sudo chown -R clickhouse:clickhouse \
  /var/lib/clickhouse \
  /var/log/clickhouse-server \
  /var/run/clickhouse-server
```

### Start ClickHouse Server manually (validation)
Start the server in the foreground to validate the setup:

```console
sudo clickhouse server --config-file=/etc/clickhouse-server/config.xml
```

Keep this terminal open.

### Connect using ClickHouse Client
Open a new terminal and run:

```console
clickhouse client
```

Run a test query:

```sql
SELECT version();
```

### Create a systemd service
Create a systemd service file to manage ClickHouse as a background service:

```console
sudo tee /etc/systemd/system/clickhouse-server.service <<'EOF'
[Unit]
Description=ClickHouse Server
After=network.target

[Service]
Type=simple
User=clickhouse
Group=clickhouse
ExecStart=/usr/bin/clickhouse server --config=/etc/clickhouse-server/config.xml
Restart=always
RestartSec=10
LimitNOFILE=1048576

[Install]
WantedBy=multi-user.target
EOF
```
Reload systemd and enable the service:

```console
sudo systemctl daemon-reload
sudo systemctl enable clickhouse-server
sudo systemctl start clickhouse-server
```

### Verify ClickHouse service
Check service status:

```console
sudo systemctl status clickhouse-server
```

### Final validation
Connect again using the client:

```console
clickhouse client
```

```sql
SELECT version();
```

ClickHouse is now successfully installed and running on SUSE Linux ARM64.
