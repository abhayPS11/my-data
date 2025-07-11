---
title: Deploy GitHub Actions Runner on GCP Arm64 and Benchmark Performance

minutes_to_complete: 60

who_is_this_for: This Learning Path is for DevOps engineers, system administrators, or developers who want to deploy GitHub Actions Runner on GCP Arm64 instances and analyze its performance using simple CI workflows.

learning_objectives:
       - Provision an Arm64 VM on Google Cloud Platform using the Tau T2A instance family.
       - Set up and validate a GitHub Actions self-hosted runner on the Arm64 VM.
       - Deploy a basic CI workflow and verify execution on Arm64 infrastructure.
       - Benchmark job execution and compare performance with x86-based systems.

prerequisites:
     - A Google Cloud Platform (GCP) account with billing enabled.
     - Access to Tau T2A VMs (e.g., t2a-standard-2) that support Arm64 architecture.
     - Familiarity with GitHub Actions and Linux command line.
     - Basic understanding of Docker and shell scripting.

author: Abhay Katiyar

### Tags
skilllevels: Intermediate
subjects: CI-CD
cloud_service_providers: Google Cloud Platform

armips:
  - Neoverse-N1

tools_software_languages:
  - GitHub Actions
  - Docker
  - Shell

operatingsystems:
    - Linux

further_reading:
  - resource:
      title: GitHub Actions Runner documentation
      link: https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners
      type: documentation

  - resource:
      title: Google Cloud Compute Engine
      link: https://cloud.google.com/compute/docs/instances
      type: documentation

  - resource:
      title: GitHub Actions workflows
      link: https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions
      type: documentation

   - resource:
      title: GitHub on Arm64 with Docker
      link: https://github.com/actions/runner
      type: source

### FIXED, DO NOT MODIFY
# ================================================================================
weight: 1                       # _index.md always has weight of 1 to order correctly
layout: "learningpathall"       # All files under learning paths have this same wrapper
learning_path_main_page: "yes"  # This should be surfaced when looking for related content. Only set for _index.md of learning path content.
---
