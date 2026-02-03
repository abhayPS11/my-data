# 🚀 OpenTelemetry on Arm (GCP SUSE ARM64) – Learning Path (Part 1: Environment & Application Setup)

---

## 🎯 Learning Objective

In this part, you will prepare a SUSE ARM64 VM on GCP, install container tooling, and build an instrumented Python Flask microservice that emits OpenTelemetry metrics and traces.

---

## 🧱 Target Platform

**GCP SUSE ARM64 Virtual Machine**

---

## 📌 Architecture Overview

```
Flask Microservice (ARM)
        |
        | OpenTelemetry SDK
        v
OpenTelemetry Collector
```

This part focuses on preparing the application and base environment.

---

## 🔓 Required Firewall Ports

| Service   | Port | Description         |
| --------- | ---- | ------------------- |
| Flask App | 8080 | Application traffic |

---

# ✅ Step 1: Install Docker on SUSE ARM64

### Summary

Installs Docker Engine to run containerized workloads on the ARM-based SUSE VM.

```bash
sudo zypper refresh
sudo zypper install -y docker
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
newgrp docker
```

Verify installation:

```bash
docker --version
```

---

# ✅ Step 2: Install Docker Compose (Standalone)

### Summary

Installs Docker Compose v2 binary for orchestrating multi-container applications.

```bash
sudo curl -L https://github.com/docker/compose/releases/download/v2.27.0/docker-compose-linux-aarch64 \
  -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```

Verify:

```bash
docker-compose --version
```

---

# 📁 Step 3: Create Project Directory

### Summary

Creates a workspace for all OpenTelemetry demo files.

```bash
mkdir ~/otel-demo
cd ~/otel-demo
```

---

# 🐍 Step 4: Create Instrumented Flask Microservice

### Summary

Creates a Python Flask application integrated with OpenTelemetry SDK to generate traces and metrics.

Create file:

```bash
vi app.py
```

#### File: app.py

* Initializes OpenTelemetry resource
* Configures trace exporter to Collector
* Configures metric exporter
* Instruments Flask automatically
* Exposes a simple HTTP endpoint

---

# 📦 Step 5: Create Python Dependencies File

### Summary

Defines required Python libraries for Flask and OpenTelemetry.

```bash
nano requirements.txt
```

#### File: requirements.txt

Includes:

* Flask framework
* OpenTelemetry API & SDK
* OTLP exporters
* Flask instrumentation

---

# 🐳 Step 6: Create Application Dockerfile

### Summary

Builds a lightweight Python container image for the Flask service on ARM64.

```bash
nano Dockerfile
```

#### File: Dockerfile

* Uses Python 3.10 slim base image
* Installs dependencies
* Copies application code
* Runs Flask app

---

## ✅ End of Part 1

You now have:

✔ Docker & Compose installed on ARM
✔ An instrumented Flask microservice
✔ Container build configuration

➡️ Continue to **Part 2: Observability Stack & Deployment** to connect OpenTelemetry Collector, Prometheus, and Jaeger.
