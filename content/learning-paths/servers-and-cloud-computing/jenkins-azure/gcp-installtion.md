---
title: Install Jenkins
weight: 7

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Jenkins on GCP SUSE Arm64
This section covers the installation of **Jenkins (Latest Stable LTS)** on a **GCP SUSE Linux Enterprise Server (SLES) Arm64 VM**. The goal is to prepare a clean, ARM-native Jenkins environment that will be used later for CI/CD use cases.

### System Preparation
Update the system and install required utilities.

```console
sudo zypper refresh
sudo zypper update -y
sudo zypper install -y curl wget ca-certificates gnupg
```

### Install Java 17 (Required)
Jenkins LTS officially requires Java 17.

```console
sudo zypper install -y java-17-openjdk java-17-openjdk-devel
```

Verify Java installation:

```console
java -version
```

Expected output includes:

```text
openjdk version "17"
```

### Add Jenkins Official Repository (Stable LTS)
Import the Jenkins repository signing key:

```console
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```

Add the Jenkins stable repository:

```console
sudo zypper addrepo https://pkg.jenkins.io/redhat-stable/ jenkins
sudo zypper refresh
```

### Install Jenkins (Latest Stable LTS)

```console
sudo zypper install -y jenkins
```

This installs the **latest Jenkins LTS** available at install time.

### Start and Enable Jenkins Service
Enable Jenkins to start automatically on boot and start the service.

```console
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

Verify service status:

```console
sudo systemctl status jenkins
```

Expected state:

```text
Active: active (running)
```

## Step 6: Verify Jenkins Version

```console
jenkins --version
```

This section completes the installation of Jenkins LTS on a GCP SUSE Arm64 VM using Java 17. Jenkins service health, version validation, network accessibility, and initial UI setup are verified. The system is now ready for ARM-native CI/CD use cases.
