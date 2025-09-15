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
