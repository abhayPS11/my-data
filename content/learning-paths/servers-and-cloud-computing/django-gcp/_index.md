---
title: Deploy Django on GKE using Google Axion (Arm64)

minutes_to_complete: 90

who_is_this_for: This Learning Path is designed for DevOps engineers and software developers who want to deploy, operate, and benchmark a production-grade Django REST API on Google Kubernetes Engine (GKE) running on Arm64 Axion processors, integrated with managed Google Cloud data services.

learning_objectives:
  - Provision an Arm-based SUSE Linux Enterprise Server (SLES) virtual machine on Google Cloud (C4A with Axion processors)
  - Provision Google Axion (Arm64) compute using GKE node pools
  - Package a Django REST API into an Arm-native Docker container
  - Push container images to Google Artifact Registry
  - Deploy Django on GKE using Kubernetes manifests (Deployment, Service, ConfigMap, Secrets)
  - Integrate Django with Cloud SQL (PostgreSQL) over private IP
  - Integrate Django with Memorystore (Redis) for caching and sessions
  - Expose Django using a Kubernetes LoadBalancer
  - Validate application connectivity to PostgreSQL and Redis
  - Measure throughput and p95 latency using ApacheBench against Gunicorn on Arm

prerequisites:
  - A Google Cloud Platform (GCP) account with billing enabled
  - Basic familiarity with Linux, Docker, and Kubernetes
  - Basic knowledge of Django and Python

author: Pareena Verma

##### Tags
skilllevels: Intermediate
subjects: Cloud Native, Web
cloud_service_providers: Google Cloud

armips:
  - Axion (Neoverse-based Arm64)

tools_software_languages:
  - Django
  - Docker
  - Kubernetes
  - Google Artifact Registry
  - Cloud SQL (PostgreSQL)
  - Memorystore (Redis)

operatingsystems:
  - Linux (Arm64)

# ================================================================================
#       FIXED, DO NOT MODIFY
# ================================================================================
further_reading:
  - resource:
      title: Google Kubernetes Engine
      link: https://cloud.google.com/kubernetes-engine
      type: documentation

  - resource:
      title: Cloud SQL for PostgreSQL
      link: https://cloud.google.com/sql/docs/postgres
      type: documentation

  - resource:
      title: Memorystore for Redis
      link: https://cloud.google.com/memorystore/docs/redis
      type: documentation

  - resource:
      title: Django documentation
      link: https://docs.djangoproject.com/
      type: documentation

  - resource:
      title: ApacheBench
      link: https://httpd.apache.org/docs/2.4/programs/ab.html
      type: documentation

weight: 1
layout: "learningpathall"
learning_path_main_page: "yes"
---
