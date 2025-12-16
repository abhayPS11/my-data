---
title: Install Jenkins
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## Install Jenkins on Azure Cobalt 100
This guide explains how to install **Jenkins (Latest Stable LTS)** on an **Azure Ubuntu 24.04 LTS Arm64 VM**.

At the end of this guide, Jenkins will be:

* Installed and running as a system service
* Accessible on **port 8080**
* Verified on **Arm64 (aarch64)** with **Java 17**

### System Preparation
Update the system and install required utilities.

```console
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget gnupg ca-certificates
```

These tools are required to securely download Jenkins packages.

### Install Java 17 (Required)
Jenkins LTS officially supports **Java 17**.

```console
sudo apt install -y openjdk-17-jdk
```

### Verify Java Installation

```console
java -version
```

You should see an output similar to:
```output
openjdk version "17.0.17" 2025-10-21
OpenJDK Runtime Environment (build 17.0.17+10-Ubuntu-124.04)
OpenJDK 64-Bit Server VM (build 17.0.17+10-Ubuntu-124.04, mixed mode, sharing)
```

### Add Jenkins Official Repository (Stable LTS)

```console
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | \
sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

This key ensures Jenkins packages are trusted.

### Add Jenkins Stable Repository

```console
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | \
sudo tee /etc/apt/sources.list.d/jenkins.list
```

### Install Jenkins (Latest Stable LTS)

```console
sudo apt update
sudo apt install -y jenkins
```

This installs the **latest Jenkins LTS available** at install time.

### Start and Enable Jenkins Service
Enable Jenkins to start automatically on boot:

```console
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

### Verify Service Status

```console
sudo systemctl status jenkins
```

You should see an output similar to:
```output
Active: active (running)
```

### Verify Jenkins Version

```console
jenkins --version
```

You should see an output similar to:
```output
2.528.3
```
This confirms the installed Jenkins LTS version.

This installation confirms Jenkins LTS is successfully deployed on an Azure Ubuntu Arm64 VM.
