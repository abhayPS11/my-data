---
title: Install Dynatrace OneAgent on Azure Ubuntu Arm64 virtual machine
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Dynatrace OneAgent on Azure Ubuntu Arm64

To install Dynatrace OneAgent on an Azure Ubuntu 24.04 LTS Arm64 virtual machine, follow these steps.

At the end of the installation, Dynatrace is:

* Installed and running as a host monitoring agent
* Connected to the Dynatrace SaaS environment
* Monitoring system processes and services automatically
* Verified on Arm64 (aarch64) architecture

## Update the system and install required tools

Update the operating system and install the tools required for downloading the Dynatrace installer.

```console
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget unzip ca-certificates
```

## Verify Arm64 architecture

Confirm that the virtual machine is running on the Arm64 architecture.

```console
uname -m
```

output is similar to:
```output
aarch64
```
This confirms the system is using the Arm64 architecture required for Cobalt 100 processors.

## Step 1 — Log in to Dynatrace

Open your Dynatrace environment:

```console
https://<ENVIRONMENT-ID>.live.dynatrace.com
```

Example:

```text
https://qzo72404.live.dynatrace.com
```

## Navigate to Deployment

In the Dynatrace UI:

Select oneagent then select setup

Cloud platform → Linux

## Select ARM Architecture

In the installer page:

- Select architecture → ARM64
- Select monitoring mode:
  >Full-stack monitoring

## Copy OneAgent Installer Command

Dynatrace will generate a command like this:

```console
wget -O Dynatrace-OneAgent-Linux-arm.sh \
"https://<ENV>.live.dynatrace.com/api/v1/deployment/installer/agent/unix/default/latest?arch=arm" \
--header="Authorization: Api-Token <API_TOKEN>"
```

**Example:**

```text
wget -O Dynatrace-OneAgent-Linux-arm.sh \
"https://qzo72404.live.dynatrace.com/api/v1/deployment/installer/agent/unix/default/latest?arch=arm" \
--header="Authorization: Api-Token DT_API_TOKEN"
```

**Verify signature**

```console
wget https://ca.dynatrace.com/dt-root.cert.pem ; ( echo 'Content-Type: multipart/signed; protocol="application/x-pkcs7-signature"; micalg="sha-256"; boundary="--SIGNED-INSTALLER"'; echo ; echo ; echo '----SIGNED-INSTALLER' ; cat Dynatrace-OneAgent-Linux-x86-1.331.49.20260227-104933.sh ) | openssl cms -verify -CAfile dt-root.cert.pem > /dev/null
```
Run it on the VM.

## Install OneAgent as the privileged user

Run:

```console
sudo /bin/sh Dynatrace-OneAgent-Linux-x86-1.331.49.20260227-104933.sh --set-monitoring-mode=fullstack --set-app-log-content-access=true
```

```output
2026-03-12 05:59:21 UTC Starting Dynatrace ActiveGate AutoUpdater...
2026-03-12 05:59:21 UTC Checking if Dynatrace ActiveGate AutoUpdater is running ...
2026-03-12 05:59:21 UTC Dynatrace ActiveGate AutoUpdater is running.
2026-03-12 05:59:21 UTC Cleaning autobackup...
2026-03-12 05:59:21 UTC Removing old installation log files...
2026-03-12 05:59:21 UTC
2026-03-12 05:59:21 UTC --------------------------------------------------------------
2026-03-12 05:59:21 UTC Installation finished successfully.
```

Installer performs:

- kernel module setup
- service installation
- environment configuration

## Verify OneAgent Service

```console
sudo systemctl status oneagent
```

```output
● dynatracegateway.service - Dynatrace ActiveGate service
     Loaded: loaded (/etc/systemd/system/dynatracegateway.service; enabled; preset: enabled)
     Active: active (running) since Thu 2026-03-12 05:59:07 UTC; 1min 7s ago
    Process: 20280 ExecStart=/opt/dynatrace/gateway/dynatracegateway start (code=exited, status=0/SUCCESS)
   Main PID: 20316 (dynatracegatewa)
```

## Verify Dynatrace Processes

```console
ps aux | grep oneagent
```

```output
dtuser     17754  0.0  0.0 307872  4388 ?        Ssl  05:48   0:00 /opt/dynatrace/oneagent/agent/lib64/oneagentwatchdog -bg -config=/opt/dynatrace/oneagent/agent/conf/watchdog.conf
dtuser     17761  0.2  0.3 1183000 59136 ?       Sl   05:48   0:06 oneagentos -Dcom.compuware.apm.WatchDogTimeout=900 -watchdog.restart_file_location=/var/lib/dynatrace/oneagent/agent/watchdog/watchdog_restart_file -Dcom.compuware.apm.WatchDogPipe=/var/lib/dynatrace/oneagent/agent/watchdog/oneagentos_pipe_17754
dtuser     17793  0.0  0.2 689184 34408 ?        Sl   05:48   0:01 oneagentloganalytics -Dcom.compuware.apm.WatchDogTimeout=900 -Dcom.compuware.apm.WatchDogPipe=/var/lib/dynatrace/oneagent/agent/watchdog/oneagentloganalytics_pipe_17754
dtuser     17795  0.1  0.2 361936 42940 ?        Sl   05:48   0:04 oneagentnetwork -Dcom.compuware.apm.WatchDogTimeout=900 -Dcom.compuware.apm.WatchDogPipe=/var/lib/dynatrace/oneagent/agent/watchdog/oneagentnetwork_pipe_17754
dtuser     17883  0.0  0.0  28212  5340 ?        Sl   05:49   0:00 /opt/dynatrace/oneagent/agent/lib64/oneagentebpfdiscovery --log-dir /var/log/dynatrace/oneagent/os/ --log-no-stdout --log-level info
azureus+   23847  0.0  0.0   9988  2772 pts/0    S+   06:33   0:00 grep --color=auto oneagent
```

## Confirm Host Detection in Dynatrace

In the Dynatrace UI go to:

```text
Infrastructure & Operations
→ Hosts
```

You should see:

```outout
Host name: <vm-name>
OS: Linux
Architecture: ARM64
Monitoring mode: Full Stack
```

## Check Automatic Process Discovery

Dynatrace automatically discovers running services.

View them under:

```text
Hosts → Processes
```

## What you've accomplished and what's next

You've successfully installed Dynatrace OneAgent on your Azure Ubuntu Arm64 virtual machine. Your installation includes:

- Dynatrace OneAgent is installed and running as a system service
- Automatic startup enabled through systemd
- Secure connection to the Dynatrace SaaS platform
- Full-stack monitoring of system resources and processes
- Arm64-native monitoring on Azure Cobalt 100 processors

Next, you'll install Dynatrace ActiveGate to enable advanced features such as Kubernetes monitoring, secure data routing, and extension support.
