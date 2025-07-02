---
title: "Background"

weight: 2

layout: "learningpathall"
---

## Why Convert AArch64 ISO to VHD for Azure?

Azure supports custom image deployment through its Shared Image Gallery, allowing users to run their preferred or community Linux distributions—even if they are not officially available in the Azure Marketplace. For Arm64-based workloads, this becomes particularly valuable when working with custom-built operating systems or community-supported AArch64 images.

By converting an AArch64 `.iso` file into a `.vhd` (Virtual Hard Disk), developers and system administrators can take advantage of Microsoft Azure’s Arm64 infrastructure, specifically the **Cobalt 100** processor-based VMs. This enables testing, development, and deployment of specialized Linux environments at scale on cloud-native Arm hardware.

## Use Case: Custom Arm64 Linux Images on Azure

Many open-source Linux distributions support AArch64 architecture but do not provide pre-built Azure-compatible images. With this Learning Path, you can:

- Use QEMU to install the OS from ISO into a disk image.
- Convert the image into VHD format compatible with Azure.
- Upload it to Azure and deploy a VM using the Shared Image Gallery.

This process gives you flexibility and control over the OS environment while benefiting from the performance and cost advantages of Arm-based cloud computing.

To learn more about Azure’s Cobalt 100 VMs, read the blog:  
[Announcing the preview of new Azure VMs based on the Azure Cobalt 100 processor](https://techcommunity.microsoft.com/blog/azurecompute/announcing-the-preview-of-new-azure-vms-based-on-the-azure-cobalt-100-processor/4146353).
