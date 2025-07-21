---
title: Deploy Apache Spark on Google Axion C4A VM

minutes_to_complete: 60

who_is_this_for: This is an advanced topic for anyone who wants to deploy Spark on GCP C4A Arm64 VMs, including installation, baseline testing, and benchmarking. 

learning_objectives:
       - Provision an Arm VM on the Google Cloud Platform using the C4A Google Axion instance family.
       - Understand how to install and configure Apache Spark on Arm-based GCP C4A instances.
       - Deploy Spark in local mode and validate functionality through baseline testing.
       - Perform benchmarking to evaluate and compare Apache Spark’s performance on both x86_64 and Arm.

prerequisites:
     - A [Google Cloud Platform (GCP)](https://cloud.google.com/free?utm_source=google&hl=en) account with billing enabled.
     - Basic understanding of Linux command line and shell scripting.
     - Familiarity with distributed computing concepts and the [Apache Spark architecture](https://spark.apache.org/docs/latest/). 

author: Zach Lasiuk

##### Tags
skilllevels: Advanced
subjects: 
cloud_service_providers: Google Cloud

armips:
    - Neoverse-V2

tools_software_languages:
  - Apache Spark
  - OpenJDK
  - Scala
  - Maven

operatingsystems:
    - Linux

# ================================================================================
#       FIXED, DO NOT MODIFY
# ================================================================================
further_reading:
    - resource:
        title: Google Cloud official website and documentation
        link: https://cloud.google.com/docs
        type: documentation

    - resource:
        title: Spark official website and documentation
        link: https://spark.apache.org/
        type: documentation

    - resource:
        title: The Scala programming language official website
        link: scala-lang.org
        type: website


weight: 1                       # _index.md always has weight of 1 to order correctly
layout: "learningpathall"       # All files under learning paths have this same wrapper
learning_path_main_page: "yes"  # Indicates this should be surfaced when looking for related content. Only set for _index.md of learning path content.
---
