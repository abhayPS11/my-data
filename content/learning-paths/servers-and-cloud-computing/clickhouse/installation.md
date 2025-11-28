---
title: Install ClickHouse
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install ClickHouse on GCP VM

### Install required system packages
Refresh system repositories and install basic utilities needed to download and run ClickHouse.

```console
sudo zypper refresh
sudo zypper install -y curl tar gzip sudo
```

### Download ClickHouse using the official installer
Download the official ClickHouse installation script, which works for both x86 and ARM64 systems.

```console
curl https://clickhouse.com/ | sh
```
This command downloads the ClickHouse binary into the current directory.

### Install ClickHouse components
Run the installer with root privileges to install all ClickHouse components.

```console
sudo ./clickhouse install
```

This installs:

- **ClickHouse Server** – Runs the core database engine and handles all data storage, queries, and processing.
- **ClickHouse Client** – Provides a command-line interface to connect to the server and run SQL queries.
- **ClickHouse Local** – Allows running SQL queries on local files without starting a server.
- **Default configuration files (/etc/clickhouse-server)** – Stores server settings such as ports, users, storage paths, and performance tuning options.

### Verify the installed version
Confirm that all ClickHouse components are installed correctly by checking their versions.

```console
clickhouse --version
clickhouse server --version
clickhouse client --version
clickhouse local --version
```

### Create ClickHouse user and directories
Create a dedicated system user and required directories for data, logs, and runtime files.

```console
sudo useradd -r -s /sbin/nologin clickhouse || true
sudo mkdir -p /var/lib/clickhouse
sudo mkdir -p /var/log/clickhouse-server
sudo mkdir -p /var/run/clickhouse-server
```
Set proper ownership so ClickHouse can access these directories.

```console
sudo chown -R clickhouse:clickhouse \
  /var/lib/clickhouse \
  /var/log/clickhouse-server \
  /var/run/clickhouse-server
```

### Start ClickHouse Server manually (validation)
You can just run the ClickHouse server in the foreground to confirm the configuration is valid.

```console
sudo clickhouse server --config-file=/etc/clickhouse-server/config.xml
```
Keep this terminal open while testing.

### Start clickhouse-server with:
Start ClickHouse using the built-in start command for normal operation.

```console
sudo clickhouse start
```
### Connect using ClickHouse Client
Open a new terminal and connect to the ClickHouse server.

```console
clickhouse client
```
Run a test query to confirm connectivity.

```sql
SELECT version();
```

### Create a systemd service
Set up ClickHouse as a system service so it starts automatically on boot.

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
**Reload systemd and enable the service:**

```console
sudo systemctl daemon-reload
sudo systemctl enable clickhouse-server
sudo systemctl start clickhouse-server
```

### Verify ClickHouse service
Ensure the ClickHouse server is running correctly as a background service.

```console
sudo systemctl status clickhouse-server
```

### Final validation
Reconnect to ClickHouse and confirm it is operational.

```console
clickhouse client
```

```sql
SELECT version();
```

ClickHouse is now successfully installed, configured, and running on SUSE Linux Arm64 with automatic startup enabled.
