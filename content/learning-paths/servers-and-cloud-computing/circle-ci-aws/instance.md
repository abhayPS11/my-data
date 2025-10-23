---
title: Create an AWS EC2 Arm64 Graviton2 Instance
weight: 3

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Overview

In this section, you will learn how to provision a Google Axion C4A Arm virtual machine on Google Cloud Platform (GCP) using the `c4a-standard-4` (4 vCPUs, 16 GB memory) machine type in the Google Cloud Console.  

{{% notice Note %}}
For support on AWS setup, see the Learning Path [Getting started with AWS](https://learn.arm.com/learning-paths/servers-and-cloud-computing/csp/aws/).
{{% /notice %}}

## Provision an AWS EC2 Arm64 Graviton2 Instance in the AWS Management Console

To create a virtual machine based on the AWS Graviton2 Instance type:  
- Navigate to the [AWS Management Console](https://aws.amazon.com/console/).  
- Go to **EC2 > Instances** and select **Launch Instance**.  
- Under **Instance configuration**:  
   - Enter an appropriate **Instance name**.  
   - Choose an **Amazon Machine Image (AMI)** such as **Ubuntu 24.04 ARM64**.

        ![Create a Google Axion C4A Arm virtual machine in the Google Cloud Console with c4a-standard-4 selected alt-text#center](images/gcp-vm.png "Creating a Google Axion C4A Arm virtual machine in Google Cloud Console")

   - Under **Instance type**, select a Graviton2-based type `m6g.large`.  
   - Configure **Key pair**, **Network settings**, and **Storage** as required.  
   - Click **Launch Instance** to create the virtual machine.  


   ![Create a Google Axion C4A Arm virtual machine in the Google Cloud Console with c4a-standard-4 selected alt-text#center](images/gcp-vm.png "Creating a Google Axion C4A Arm virtual machine in Google Cloud Console")

- Under **OS and Storage**, select **Change**, then choose an Arm64-based OS image. For this Learning Path, use **SUSE Linux Enterprise Server**. Pick the preferred version for your Operating System. Ensure you select the **Arm image** variant. Click **Select**.
- Under **Networking**, enable **Allow HTTP traffic**.
- Click **Create** to launch the instance.
