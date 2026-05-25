---
title: Deploy Keycloak on Azure Cobalt 100 Arm64 virtual machines
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Deploy Keycloak on Azure Cobalt 100 Arm64 VM

## Overview

This learning path explains how to deploy and configure Keycloak on Azure Cobalt 100 Arm64 virtual machines using PostgreSQL as the backend database.

You will learn how to:

- Install Keycloak on Arm64 infrastructure
- Configure PostgreSQL integration
- Bootstrap the Keycloak admin user
- Configure Keycloak as a systemd service
- Fix common Keycloak 26.x issues
- Access the Keycloak administration console

## What is Keycloak?

Keycloak is an open-source Identity and Access Management (IAM) platform.

It provides:

- Single Sign-On (SSO)
- OAuth2 authentication
- OpenID Connect (OIDC)
- SAML authentication
- Multi-factor authentication
- Role-based access control
- User federation

## Target platform

| Component      | Value                         |
| -------------- | ----------------------------- |
| Cloud          | Microsoft Azure               |
| VM Type        | Azure Cobalt 100 Arm64        |
| Recommended VM | Standard_D4ps_v6              |
| OS             | Ubuntu Server 24.04 LTS Arm64 |

## Architecture

```text
Azure Cobalt 100 Arm64 VM
        |
        ├── PostgreSQL
        ├── Keycloak
        ├── Flask OAuth Demo App
        └── Browser Access
```

## Configure Azure networking

Allow these inbound ports:

| Port | Purpose                      |
| ---- | ---------------------------- |
| 22   | SSH                          |
| 8080 | Keycloak HTTP                |
| 9000 | Keycloak management endpoint |
| 5000 | Flask demo application       |


## Connect to VM

```bash
ssh azureuser@YOUR_PUBLIC_IP
```

## Update packages

```bash
sudo apt update && sudo apt upgrade -y
```

## Install dependencies

```bash
sudo apt install -y \
openjdk-21-jdk \
postgresql \
postgresql-contrib \
python3-pip \
python3-venv \
curl \
wget \
tar
```

## Verify Java

```bash
java -version
```

Expected:

```output
openjdk version "21"
```


## Configure PostgreSQL

Login to PostgreSQL:

```bash
sudo -u postgres psql
```

Create database:

```sql
CREATE DATABASE keycloak;
```

Create user:

```sql
CREATE USER keycloakuser WITH PASSWORD 'StrongPassword123!';
```

Grant privileges:

```sql
GRANT ALL PRIVILEGES ON DATABASE keycloak TO keycloakuser;
```

Connect to Keycloak database:

```sql
\c keycloak
```

Grant schema permissions:

```sql
GRANT ALL ON SCHEMA public TO keycloakuser;
ALTER SCHEMA public OWNER TO keycloakuser;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO keycloakuser;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO keycloakuser;
```

Exit PostgreSQL:

```sql
\q
```

## Download Keycloak

```bash
cd ~
wget https://github.com/keycloak/keycloak/releases/download/26.2.5/keycloak-26.2.5.tar.gz
tar -xzf keycloak-26.2.5.tar.gz
sudo mv keycloak-26.2.5 /opt/keycloak
```

## Create Keycloak Linux user

```bash
sudo useradd -r -s /bin/false keycloak
sudo chown -R keycloak:keycloak /opt/keycloak
```

## Configure Keycloak

Replace `YOUR_PUBLIC_IP` with your VM public IP.

Example:

```text
20.x.x.x
```

Create configuration:

```bash
sudo tee /opt/keycloak/conf/keycloak.conf > /dev/null <<EOF
db=postgres
db-url=jdbc:postgresql://localhost:5432/keycloak
db-username=keycloakuser
db-password=StrongPassword123!

hostname=http://YOUR_PUBLIC_IP:8080
hostname-strict=false

http-enabled=true
http-port=8080

health-enabled=true
metrics-enabled=true
EOF
```

Important:

Do NOT use:

```ini
proxy=edge
```

because it causes issues in newer Keycloak versions.

## Build Keycloak

```bash
sudo /opt/keycloak/bin/kc.sh build
```

## Bootstrap admin user

```bash
sudo KC_BOOTSTRAP_ADMIN_USERNAME=admin \
KC_BOOTSTRAP_ADMIN_PASSWORD=AdminPassword123! \
/opt/keycloak/bin/kc.sh bootstrap-admin user --optimized
```

When prompted:

```text
Enter username [temp-admin]:
```

Press Enter.

Then enter:

```text
AdminPassword123!
```

twice.

Successful output includes:

```text
Created temporary admin user with username admin
```

## Create systemd service

```bash
sudo tee /etc/systemd/system/keycloak.service > /dev/null <<EOF
[Unit]
Description=Keycloak Server
After=network.target postgresql.service
Requires=postgresql.service

[Service]
Type=simple
User=keycloak
Group=keycloak
WorkingDirectory=/opt/keycloak
ExecStart=/opt/keycloak/bin/kc.sh start --optimized
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
EOF
```

## Create required directories

```bash
sudo mkdir -p /opt/keycloak/data/tmp
sudo mkdir -p /opt/keycloak/data/log
sudo mkdir -p /opt/keycloak/data/cache
```

Fix ownership:

```bash
sudo chown -R keycloak:keycloak /opt/keycloak/data
```

Fix permissions:

```bash
sudo chmod -R 755 /opt/keycloak/data
```


## Start Keycloak

```bash
sudo systemctl daemon-reload
sudo systemctl enable keycloak
sudo systemctl start keycloak
```

Check status:

```bash
sudo systemctl status keycloak
```

Check logs:

```bash
sudo journalctl -u keycloak -f
```


## Fix HTTPS required error

If browser shows:

```text
HTTPS required
```

Run:

```bash
sudo -u postgres psql -d keycloak
```

Execute:

```sql
UPDATE realm
SET ssl_required = 'NONE'
WHERE name = 'master';
```

Exit:

```sql
\q
```

Restart Keycloak:

```bash
sudo systemctl restart keycloak
```

## Access Keycloak

Open browser:

```text
http://YOUR_PUBLIC_IP:8080/admin/
```

Login:

```text
Username: admin
Password: AdminPassword123!
```

## Verify health endpoint

```bash
curl http://localhost:9000/health
```

Expected:

```json
{"status":"UP"}
```

## What you've learned and what's next

You now have Keycloak running successfully on Azure Cobalt 100 Arm64 with PostgreSQL integration and a working admin console.

Next, you'll configure a Flask application and integrate it with Keycloak using OAuth2/OpenID Connect authentication.
