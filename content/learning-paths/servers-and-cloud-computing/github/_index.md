---
title:  Deploy GitHub Actions Runner on GCP Arm and Benchmark Performance
 
minutes_to_complete: 30

who_is_this_for: This Learning Path is for DevOps engineers, system administrators, or developers who want to deploy GitHub Actions Runner on GCP Arm64 instances and analyze its performance using simple CI workflows.

learning_objectives:
       - Provision an Arm64 VM on Google Cloud Platform using the Tau T2A instance family.
       - Set up and validate a GitHub Actions self-hosted runner on the Arm64 VM.
       - Deploy a basic CI workflow and verify execution on Arm64 infrastructure.
       - Benchmark job execution and compare performance with x86-based systems.

prerequisites:
     - A [Google Cloud Platform (GCP)](https://cloud.google.com/free?utm_source=google&hl=en) account with billing enabled.
     - Access to Tau T2A VMs (e.g., t2a-standard-2) that support Arm64 architecture.
     - Familiarity with [GitHub Actions](https://github.com/features/actions) and Linux command line.
     - Basic understanding of Docker and shell scripting.


author: 

##### Tags
skilllevels: Introductory
subjects: CI-CD
cloud_service_providers: GCP

armips:
    - Neoverse-N2

tools_software_languages:
  - GitHub Actions
  - Git cli

operatingsystems:
    - Linux

# ================================================================================
#       FIXED, DO NOT MODIFY
# ================================================================================
further_reading:
    - resource:
        title: RunsOn official website and documentation
        link: https://runs-on.com/
        type: documentation

    - resource:
        title: RunsOn installation guide
        link: https://runs-on.com/guides/install/
        type: documentation

    - resource:
        title: GitHub Actions runners benchmark for Arm
        link: https://runs-on.com/benchmarks/github-actions-runners/#arm64-runners
        type: website


weight: 1                       # _index.md always has weight of 1 to order correctly
layout: "learningpathall"       # All files under learning paths have this same wrapper
learning_path_main_page: "yes"  # Indicates this should be surfaced when looking for related content. Only set for _index.md of learning path content.
---
