---
title: "Background"

weight: 2

layout: "learningpathall"
---

## What is the C4A Machine Series (Google Cloud Axion Arm)?

The C4A series is Google Cloud's latest general-purpose VM family powered by Axion, a custom Arm Neoverse V2 CPU. It offers high performance, energy efficiency, and up to 100 Gbps networking—ideal for compute-heavy workloads like CI/CD, web servers, and ML inference.

The Google Axion VM instances are of below serie: 

* The general-purpose `C4A` virtual machine series. 

To learn more about Google Axion, refer to the blog ["Introducing Google Axion Processors, our new Arm-based CPUs"](https://cloud.google.com/blog/products/compute/introducing-googles-new-arm-based-cpu).

## GitHub Actions and CI/CD

GitHub Actions is a CI/CD platform that helps automate code build, test, and deployment workflows directly from a GitHub repository. It triggers workflows on events like push or pull requests, improving development speed and consistency. 

A key feature is [self-hosted runners](https://docs.github.com/en/actions/concepts/runners/about-self-hosted-runners), which let teams run workflows on their own machines—including Arm64-based systems—for better hardware control and architecture-specific builds. This is useful for optimizing performance and ensuring compatibility on Arm64 environments. Self-hosted runners also allow private network use and faster execution by leveraging custom or high-performance hardware.
