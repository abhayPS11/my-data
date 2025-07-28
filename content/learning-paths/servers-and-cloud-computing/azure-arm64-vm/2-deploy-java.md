---
title: Install Java on Microsoft Azure Virtual Machine with Cobalt 100 (Arm64) Processors
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---


### Platform Overview
Whether you're using an Azure Linux 3.0 Docker container or a virtual machine created from a custom Azure Linux 3.0 image, the deployment and benchmarking steps remain the same.

### Working inside Azure Linux 3.0 Docker container
The Azure Linux Container Host is an operating system image that's optimized for running container workloads on Azure Kubernetes Service (AKS). Microsoft maintains the Azure Linux Container Host and based it on CBL-Mariner, an open-source Linux 
distribution created by Microsoft. 
To know more about Azure Linux 3.0, kindly refer [What is Azure Linux Container Host for AKS](https://learn.microsoft.com/en-us/azure/azure-linux/intro-azure-linux). Azure Linux 3.0 offers support for Aarch64. However, the standalone virtual machine image for Azure Linux 3.0 or CBL Mariner 3.0 is not available for Arm.

Hence, to use the default software stack provided by the Microsoft team, this guide will focus on creating a docker container with Azure Linux 3.0 as a base image and will build 
and run the Java application inside the container, with the default JDK provided by the Microsoft team via Azure Linux 3.0 environment. 

### Create Azure Linux 3.0 Docker Container 
The [Microsoft Artifact Registry](https://mcr.microsoft.com/en-us/artifact/mar/azurelinux/base/core/about) offers updated docker image for the Azure Linux 3.0.  

To create a docker container, install docker, and then follow the below instructions: 

```console
$ sudo docker run -it --rm mcr.microsoft.com/azurelinux/base/core:3.0
``` 

The default container startup command is bash. tdnf and dnf are the default package managers.

### Install Java

This Azure Linux 3.0 image does not include Java, so you need to install it.  

First update tdnf:

```console
$ tdnf update -y 
``` 
Then install java-devel:

```console
$ tdnf install -y java-devel  
```
 
Java-devel installs both the default JRE and JDK provided by Azure Linux 3.0.

Check to ensure that the JRE is properly installed: 

```console
$ java -version 
``` 

**Your output will look like this:** 

```output
openjdk version "11.0.27" 2025-04-15 LTS 
OpenJDK Runtime Environment Microsoft-11371464 (build 11.0.27+6-LTS) 
OpenJDK 64-Bit Server VM Microsoft-11371464 (build 11.0.27+6-LTS, mixed mode, 
sharing) 
```

**Check to ensure that the JDK is properly installed:**

```console
$ javac -version 
```
Your output will look like this:

```output
javac 11.0.27 
```

Set Java Environment Variable for Arm: 

```console 
$ export JAVA_HOME=/usr/lib/jvm/msopenjdk-11 
$ export PATH=$JAVA_HOME/bin:$PATH 
```
 
{{% notice Note %}}
Azure Linux 3.0 offers the default JDK version 11.0.27. It’s important to ensure that your version of OpenJDK for Arm is at least 11.0.9, or above. There is a large performance gap between OpenJDK-11.0.8 and OpenJDK 11.0.9. A patch added in 11.0.9 reduces false-sharing cache contention. 
For more information, you can view this [Arm community blog](https://community.arm.com/arm-community-blogs/b/architectures-and-processors-blog/posts/java-performance-on-neoverse-n1). 

The [Arm Ecosystem Dashboard](https://developer.arm.com/ecosystem-dashboard/) also recommends Java/OpenJDK version 11.0.9 as minimum recommended on the Arm platforms.
{{% /notice %}}

Java installation is complete. You can now proceed with baseline testing.
