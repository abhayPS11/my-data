---
title: Deploy Multi-Architecture Docker Images with Buildkite on GCP C4A (Arm-based Axion VMs)

minutes_to_complete: 40

who_is_this_for: This is an introductory topic for software developers migrating Buildkite workloads from x86_64 to Arm-based servers, specifically on Google Cloud C4A virtual machines built on Axion processors.


learning_objectives:
  - Provision an Arm-based Virtual Machine (SUSE SLES) on Google Cloud Platform (GCP)
  - Install and configure the Buildkite Agent on an Arm64 instance
  - Set up Docker and Buildx for multi-architecture builds
  - Create and test a simple microservice application with Docker and Buildkite
  - Securely connect the Buildkite Agent to a GitHub repository using deploy keys
  - Define and run Buildkite pipelines for multi-architecture Docker image builds

prerequisites:
  - A [Google Cloud Platform (GCP)](https://cloud.google.com/free?utm_source=google&hl=en) account with billing enabled (or another supported cloud provider like Azure or AWS)
  - Basic knowledge of Linux system administration (creating users, installing packages, managing services)
  - Familiarity with [Docker](https://docs.docker.com/get-started/) and container concepts
  - A [GitHub](https://github.com/) account to host your application repository
  - Familiarity with [Buildkite concepts](https://buildkite.com/docs/tutorials/getting-started) such as agents, pipelines, secrets, and queues


author: Pareena Verma

##### Tags
skilllevels: Introductory
subjects: CI-CD
cloud_service_providers: Google Cloud

armips:
  - Neoverse

tools_software_languages:
  - Buildkite
  - Docker
  - Buildx

operatingsystems:
  - Linux

# ================================================================================
#       FIXED, DO NOT MODIFY
# ================================================================================
further_reading:
  - resource:
      title: Google Cloud documentation
      link: https://cloud.google.com/docs
      type: documentation

  - resource:
      title: Buildkite documentation
      link: https://buildkite.com/docs
      type: documentation

  - resource:
      title: Docker documentation
      link: https://docs.docker.com/
      type: documentation

weight: 1
layout: "learningpathall"
learning_path_main_page: "yes"
---
