---
title: Install Buildkite
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Buildkite on a Google Axion C4A Arm VM
This section guides you through installing the Buildkite agent on a Google Axion C4A Arm VM, enabling it to connect to your Buildkite account and run CI/CD pipelines.

```console
sudo zypper refresh
sudo zypper install -y curl unzip
```

### Download and Install Buildkite Agent

```console
sudo sh -c "$(curl -sL https://raw.githubusercontent.com/buildkite/agent/main/install.sh)" 
```
This one-line command downloads and runs the official Buildkite installer.  
It will:  
- Grab the latest version of the agent  
- Install it into your home directory (`/home/gcpuser/.buildkite-agent`)  
- Create a default config file (`buildkite-agent.cfg`) where you’ll later add your agent token  

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
This command checks the version of the Buildkite agent and confirms it installed successfully.

```consloe
/home/gcpuser/.buildkite-agent/bin/buildkite-agent --version
```
You should see output similar to:
```output
buildkite-agent version 3.107.0+10853.4606e31391a3bad2a5ba62f421ef041c0e4f04ab
```

## Install Docker and Docker Buildx
Buildkite will use Docker to build and push images. Let’s set it up.

1. Refresh package repositories and Install required packages:

This updates your system’s software list and installs git, python3-pip and docker:

```console
sudo zypper refresh
sudo zypper install -y git python3 python3-pip docker
```

2. Enable and Start Docker

This makes sure Docker runs automatically every time, when your VM starts.
```console
sudo systemctl enable docker
sudo systemctl start docker
```

3. Optional – Allow running Docker without sudo:

By default, only `root` can run Docker. These commands add your current user (`gcpuser`) to the `docker` group so you don’t need to type `sudo` for every command.

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
Docker Buildx is a plugin that allows building multi-architecture images (e.g., both Arm64 and x86).

```console
wget https://github.com/docker/buildx/releases/download/v0.26.1/buildx-v0.26.1.linux-arm64
chmod +x buildx-v0.26.1.linux-arm64
mkdir -p ~/.docker/cli-plugins
mv buildx-v0.26.1.linux-arm64 ~/.docker/cli-plugins/docker-buildx
```

What this does:

- Downloads the latest Buildx binary for Arm64
- Makes it executable
- Moves it into Docker’s plugin directory (`~/.docker/cli-plugins/`)
- After this, you can run `docker buildx` commands
