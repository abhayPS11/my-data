---
title: Platform Overview and Container Setup
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Platform Overview
Whether you're using an Azure Linux 3.0 Docker container or a virtual machine created from a custom Azure Linux 3.0 image, the deployment and benchmarking steps remain the same.

## Working inside Azure Linux 3.0 Docker container
The Azure Linux Container Host is an operating system image that's optimized for running container workloads on Azure Kubernetes Service (AKS). Microsoft maintains the Azure Linux Container Host and based it on CBL-Mariner, an open-source Linux 
distribution created by Microsoft. 
To know more about Azure Linux 3.0, kindly refer [What is Azure Linux Container Host for AKS](https://learn.microsoft.com/en-us/azure/azure-linux/intro-azure-linux). Azure Linux 3.0 offers support for Aarch64. However, the standalone virtual machine image for Azure Linux 3.0 or CBL Mariner 3.0 is not available for Arm.

Hence, to use the default software stack provided by the Microsoft team, this guide will focus on creating a docker container with Azure Linux 3.0 as a base image and will build 
and run the onnx based application inside the container. 

### Create Azure Linux 3.0 Docker Container 
The [Microsoft Artifact Registry](https://mcr.microsoft.com/en-us/artifact/mar/azurelinux/base/core/about) offers updated docker image for the Azure Linux 3.0.  

To create a docker container, install docker, and then follow the below instructions: 

```console
sudo docker run -it --rm mcr.microsoft.com/azurelinux/base/core:3.0
``` 
The default container startup command is bash. tdnf and dnf are the default package managers.
