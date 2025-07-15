---
title: Install ONNX, Validate Model, and Measure Performance
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## Deploy GitHub Actions Runner on GCP Arm64 VM and Automate NGINX Deployment

This Learning Path is about deploying a self-hosted GitHub Actions Runner on a GCP C4A Arm64 VM running Ubuntu. It walks through installing Git and GitHub CLI, authenticating with GitHub, configuring the runner, and automating the deployment of an NGINX server using GitHub Actions.

The guide highlights how to leverage [C4A instances powered by Arm Neoverse-V2](https://cloud.google.com/blog/products/compute/try-c4a-the-first-google-axion-processor) CPUs to run CI/CD workflows efficiently, offering an Arm-native, cost-effective, and performant solution compared to traditional x86 infrastructure.

### Install Git and GitHub CLI 
```console
$ sudo apt update 
$ sudo apt install -y git gh
```
Login to GitHub
