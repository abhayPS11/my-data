---
title: "Background"

weight: 2

layout: "learningpathall"
---

## What is Cobalt 100 Arm-based processor? 

Azure’s Cobalt 100 is built on Microsoft's first-generation, in-house Arm-based processor: the Cobalt 100. Designed entirely by Microsoft and based on Arm’s Neoverse N2 architecture, this 64-bit CPU delivers improved performance and energy efficiency across a broad spectrum of cloud-native, scale-out Linux workloads. These include web and application servers, data analytics, open-source databases, caching systems, and more. Running at 3.4 GHz, the Cobalt 100 processor allocates a dedicated physical core for each vCPU, ensuring consistent and predictable performance. 

To learn more about Cobalt 100, refer to the blog [Announcing the preview of new Azure virtual machine based on the Azure Cobalt 100 processor](https://techcommunity.microsoft.com/blog/azurecompute/announcing-the-preview-of-new-azure-vms-based-on-the-azure-cobalt-100-processor/4146353).

## Introduction to Azure Linux 3.0

Azure Linux 3.0 is Microsoft's in-house, lightweight Linux distribution optimized for running cloud-native workloads on Azure. Designed with performance, security, and reliability in mind, it is fully supported by Microsoft and tailored for containers, microservices, and Kubernetes. With native support for Arm64 (Aarch64) architecture, Azure Linux 3.0 enables efficient execution of workloads on energy-efficient Arm-based infrastructure, making it a powerful choice for scalable and cost-effective cloud deployments.

As of now, the Azure Marketplace offers official virtual machine images of Azure Linux 3.0 only for x64-based architectures, published by Ntegral Inc. However, native Arm64 (Aarch64) images are not yet officially available. Hence, for this Learning Path, we create our custom Azure Linux 3.0 virtual machine image for Aarch64, using the [Aarch64 ISO for Azure Linux 3.0](https://github.com/microsoft/azurelinux#iso).

Alternatively, use the [Azure Linux 3.0 Docker container](https://learn.microsoft.com/en-us/azure/azure-linux/intro-azure-linux) on any supported platform.

For this Learning Path, we perform the deployment and benchmarking on both the Azure Linux 3.0 environments, the Docker container, as well as the custom-image-based virtual machine.

## Apache Spark

Apache Spark is an open-source, distributed computing system designed for fast and general-purpose big data processing. 

It provides high-level APIs in Java, Scala, Python, and R, and supports in-memory computation for increased performance. 

Spark is widely used for large-scale data analytics, machine learning, and real-time data processing. Learn more from the [Apache Spark official website](https://spark.apache.org/) and its [detailed official documentation](https://spark.apache.org/docs/latest/).
