---
title: Install Buildkite
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Buildkite on Azure Cobalt 100

### Install Required Packages for Buildkite

```console
sudo zypper refresh
sudo zypper install -y curl unzip
```

### Download and Install Buildkite Agent

```console
sudo mkdir -p /usr/local/bin
curl -L "https://github.com/buildkite/agent/releases/download/v3.103.1/buildkite-agent-linux-arm64.zip" -o buildkite-agent.zip
unzip buildkite-agent.zip
sudo mv buildkite-agent /usr/local/bin/
sudo chmod +x /usr/local/bin/buildkite-agent
```

Verify installation:

```consloe
buildkite-agent --version
```

```output
buildkite-agent version 3.103.1+10566.da00408a2cabb0fdfdb5a10fdec70fb034b2db02
```

## Install Docker and Enable Buildx

### Install Required Packages

1. Refresh package repositories:

```console
sudo zypper refresh
```

2. Install required packages:

```console
sudo zypper install -y git python3 python3-pip docker
```

### Enable and Start Docker

1. Enable Docker to start on boot:

```console
sudo systemctl enable docker
```

2. Start Docker service:

```console
sudo systemctl start docker
```

3. Optional – Allow running Docker without sudo:

```console
sudo usermod -aG docker $(whoami)
newgrp docker
```

4. Verify Docker installation:

```console
docker --version
docker run hello-world
```

---

## Step 3 – Enable Docker Buildx

1. Create a new Buildx builder instance:

```console
docker buildx create --name mybuilder --use
```

2. Bootstrap the builder:

```console
docker buildx inspect --bootstrap
```

3. Verify Buildx installation and list builders:

```console
docker buildx ls
```

```output
NAME/NODE        DRIVER/ENDPOINT                   STATUS    BUILDKIT   PLATFORMS
mybuilder*       docker-container
 \_ mybuilder0    \_ unix:///var/run/docker.sock   running   v0.24.0    linux/arm64
default          docker
 \_ default       \_ default                       running   v0.23.2    linux/arm64
```
