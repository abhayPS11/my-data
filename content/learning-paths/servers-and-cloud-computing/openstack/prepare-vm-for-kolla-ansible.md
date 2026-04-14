---
title: Prepare Azure Arm64 VM for Kolla-Ansible (Network and Storage Setup)
weight: 3

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Prepare VM for Kolla-Ansible Deployment

In this section, you will prepare an Azure Arm64 virtual machine for deploying OpenStack using Kolla-Ansible.

This includes:

* Adding a second network interface (NIC)
* Attaching a data disk
* Configuring networking and storage inside the VM

---

## Why this setup is required

OpenStack services require:

* Management network (`eth0`)
* External network (`eth1`)
* Storage for containers and images

---

# Step 1: Add a second NIC in Azure

## Stop the VM

* Go to Azure Portal → Virtual Machines
* Select your VM
* Click **Stop**



## Attach a new NIC

* Go to **Networking → Network settings**
* Click **Attach network interface**
* Select **Create new NIC**

---

## Configure NIC

* Keep the NIC in the same Virtual Network
* Do NOT assign a public IP
* Attach the NIC

---

## Start the VM

* Click **Start** to power on the VM

---


# Step 2: Attach a data disk in Azure

## Add disk

* Go to Azure Portal → Virtual Machine
* Select **Disks**
* Click **Add data disk**
* Select **Create disk**

---

## Configure disk

* Size: 32 GB (recommended)
* Type: Standard SSD
* Click **Apply**


## What you've accomplished

You successfully prepared the Azure Arm64 virtual machine for Kolla-Ansible by:

* Adding and configuring a second network interface
* Attaching and mounting a data disk
* Setting up Open vSwitch networking
* Preparing the system for OpenStack deployment

---

## What's next

In the next section, you will:

* Install Kolla-Ansible
* Deploy OpenStack services
* Validate the deployment

---
