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
GitHub Actions is a Continuous Integration and Continuous Deployment (CI/CD) platform that enables developers to automate their workflow directly within their GitHub repository. It facilitates the creation of workflows for building, testing, and deploying code based on various events, such as a push to a repository or a pull request. This automation streamlines the process of integrating changes and deploying them to production, fostering a more efficient and consistent development cycle.

A powerful feature of GitHub Actions is its support for self-hosted runners. Unlike the default runners hosted by GitHub, self-hosted runners allow developers to execute their CI/CD pipelines on their own hardware. This capability is vital when custom hardware configurations are necessary. For example, developers can use Arm64-based runners to build software specifically for Arm64 architectures, ensuring compatibility and optimizing performance for software intended to run on devices and servers powered by Arm64 processors.

Utilizing self-hosted runners with GitHub Actions offers flexibility and control, enabling teams to tailor the CI/CD process to meet their specific hardware and network requirements. 

Advantages include:

* The use of custom hardware configurations not available in the hosted environment
* The ability to run jobs in a private network environment
* Reduced build and deployment times by leveraging high-performance or specialized hardware. 

Integrating self-hosted runners into their development workflow allows teams to align their development, testing, and deployment processes more closely with their operational environment, thereby enhancing the efficiency and reliability of their software delivery pipeline.
