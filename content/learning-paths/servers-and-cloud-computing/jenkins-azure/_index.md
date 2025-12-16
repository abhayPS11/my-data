---
title: Deploy Jenkins on Microsoft Azure Cobalt 100 processors 

minutes_to_complete: 30   

who_is_this_for: This Learning Path is designed for software developers, DevOps engineers, and platform teams who want to deploy and validate Jenkins on Arm-based Microsoft Azure virtual machines for CI/CD workloads.

learning_objectives: 
    - Provision an Azure Arm64 virtual machine using the Azure console, with Ubuntu Pro 24.04 LTS as the base image
    - Install the latest stable Jenkins LTS with OpenJDK 17 on an Arm64 VM
    - Validate Jenkins installation through service checks, UI access, and ARM-native pipeline execution

prerequisites:
    - A [Microsoft Azure](https://azure.microsoft.com/) account with access to Cobalt 100 based instances (Dpsv6). 
    - Basic understanding of Linux command line.
    - Familiarity with CI/CD concepts and [Jenkins fundamentals](https://www.jenkins.io/doc/book/pipeline/).

author: Pareena Verma

##### Tags
skilllevels: Advanced
subjects: CI-CD
cloud_service_providers: Microsoft Azure

armips:
    - Neoverse

tools_software_languages:
  - Jenkins
  - OpenJDK 17
  - Groovy (Jenkins Pipeline)

operatingsystems:
    - Linux


# ================================================================================
#       FIXED, DO NOT MODIFY
# ================================================================================
further_reading:
    - resource:
        title: Jenkins Official Documentation
        link: https://www.jenkins.io/doc/
        type: documentation
    - resource:
        title: Jenkins Pipeline Syntax
        link: https://www.jenkins.io/doc/book/pipeline/syntax/
        type: documentation
    - resource:        
        title: Jenkins on Azure
        link: https://learn.microsoft.com/en-us/azure/developer/jenkins/
        type: documentation


weight: 1
layout: "learningpathall"
learning_path_main_page: "yes"
---
