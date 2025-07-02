---
title:  Transform AArch64 ISO to VHD and Launch an Azure Linux Arm64 VM

minutes_to_complete: 120  

who_is_this_for: This Learning Path is designed for developers and system administrators who want to run custom or community-based AArch64 Linux distributions on Microsoft Azure's Arm64 virtual machines. You'll learn how to convert an AArch64 .iso image into a .vhd format, upload it to Azure, and use it to deploy a fully functional Linux Arm64 VM. This process is ideal for scenarios where prebuilt Arm images are unavailable or when testing with custom OS builds is required.


learning_objectives:
    - Convert an AArch64 ISO image into a fixed-size VHD using QEMU tools.
    - Upload the VHD to Azure and create a custom image using Azure Shared Image Gallery.
    - Deploy and access an Azure Linux Arm64 VM based on the custom image.

prerequisites:
    - A [Microsoft Azure](https://azure.microsoft.com/) account with permission to create resources, including Cobalt 100 (Arm64) instances (Dpsv6).
    - A local Linux or macOS machine with [QEMU](https://www.qemu.org/download/) installed to emulate AArch64 and convert images.
    - An [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli) installed and authenticated on your local machine.
    
author: Jason Andrews

### Tags
skilllevels: Advanced
subjects: Linux, AArch64, Virtual Machines, QEMU, Azure CLI
cloud_service_providers: Azure


armips:
    - Neoverse N2

tools_software_languages:
  - QEMU
  - Azure CLI

operatingsystems:
    - Linux

further_reading:
  - resource:
      title: Azure Virtual Machines documentation
      link: https://learn.microsoft.com/en-us/azure/virtual-machines/
      type: documentation
  - resource:
      title: Azure Shared Image Gallery documentation
      link: https://learn.microsoft.com/en-us/azure/virtual-machines/shared-image-galleries
      type: documentation
  - resource:
      title: QEMU User Documentation
      link: https://wiki.qemu.org/Documentation
      type: documentation
  - resource:
      title: Upload a VHD to Azure and create an image
      link: https://learn.microsoft.com/en-us/azure/virtual-machines/linux/upload-vhd
      type: documentation


### FIXED, DO NOT MODIFY
# ================================================================================
weight: 1                       # _index.md always has weight of 1 to order correctly
layout: "learningpathall"       # All files under learning paths have this same wrapper
learning_path_main_page: "yes"  # This should be surfaced when looking for related content. Only set for _index.md of learning path content.
---
