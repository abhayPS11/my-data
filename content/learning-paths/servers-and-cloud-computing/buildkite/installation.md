---
title: Install Buildkite
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Buildkite on Azure Cobalt 100

```console
sudo zypper refresh
sudo zypper install -y curl unzip
```

### Download and Install Buildkite Agent

```console
sudo sh -c "$(curl -sL https://raw.githubusercontent.com/buildkite/agent/main/install.sh)" 
```
```output
  _           _ _     _ _    _ _                                _
 | |         (_) |   | | |  (_) |                              | |
 | |__  _   _ _| | __| | | ___| |_ ___    __ _  __ _  ___ _ __ | |_
 | '_ \| | | | | |/ _` | |/ / | __/ _ \  / _` |/ _` |/ _ \ '_ \| __|
 | |_) | |_| | | | (_| |   <| | ||  __/ | (_| | (_| |  __/ | | | |_
 |_.__/ \__,_|_|_|\__,_|_|\_\_|\__\___|  \__,_|\__, |\___|_| |_|\__|
                                                __/ |
                                               |___/
Finding latest release...
Installing Version: v3.107.0
Destination: /home/gcpuser/.buildkite-agent
Downloading https://github.com/buildkite/agent/releases/download/v3.107.0/buildkite-agent-linux-amd64-3.107.0.tar.gz

A default buildkite-agent.cfg has been created for you in /home/gcpuser/.buildkite-agent

Don't forget to update the config with your agent token! You can find it token on your "Agents" page in Buildkite

Successfully installed to /home/gcpuser/.buildkite-agent

You can now start the agent!

  /home/gcpuser/.buildkite-agent/bin/buildkite-agent start

For docs, help and support:

  https://buildkite.com/docs/agent/v3

Happy building! <3
```

Verify installation:

```consloe
/home/gcpuser/.buildkite-agent/bin/buildkite-agent --version
```

```output
buildkite-agent version 3.107.0+10853.4606e31391a3bad2a5ba62f421ef041c0e4f04ab
```

## Install Docker and Docker Buildx

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

5. Create Docker config dir for agent user

```console
mkdir -p /home/gcpuser/.docker
chmod 700 /home/gcpuser/.docker
```
Login to Docker Hub (saves creds into config.json)

```console
docker login
```

Verify credentials are stored

```
cat /home/gcpuser/.docker/config.json
```


##  Install Docker Buildx

```console
wget https://github.com/docker/buildx/releases/download/v0.26.1/buildx-v0.26.1.linux-arm64
chmod +x buildx-v0.26.1.linux-arm64
mkdir -p ~/.docker/cli-plugins
mv buildx-v0.26.1.linux-arm64 ~/.docker/cli-plugins/docker-buildx
```

