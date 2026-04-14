---
title: Validate OpenStack Deployment and Launch VM on Azure Arm64
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Validate OpenStack Deployment and Launch a Virtual Machine

This guide verifies your deployment and launches a test VM.



## Install OpenStack CLI (if not installed)

```console
pip install python-openstackclient
```


## Load admin credentials

```console
source /etc/kolla/admin-openrc.sh
```


## Verify services

```console
openstack compute service list
openstack network agent list
```

Ensure all services are UP.


## Upload image

```console
wget https://cloud.debian.org/images/cloud/bookworm/latest/debian-12-genericcloud-arm64.qcow2

openstack image create "test-image" \
  --file debian-12-genericcloud-arm64.qcow2 \
  --disk-format qcow2 \
  --container-format bare \
  --public
```

## Create network

```console
openstack network create test-net

openstack subnet create test-subnet \
  --network test-net \
  --subnet-range 192.168.0.0/24
```


## Create flavor

```console
openstack flavor create m1.tiny --ram 512 --disk 5 --vcpus 1
```


## Launch VM

```console
openstack server create \
  --flavor m1.tiny \
  --image test-image \
  --network test-net \
  test-vm
```


## Verify VM status

```console
watch -n 2 openstack server list
```

Expected:

```text
Every 2.0s: openstack server list                                                               ansible-d8ps: Tue Apr 14 07:41:01 2026

+--------------------------------------+---------+--------+-----------------------+------------+---------+
| ID                                   | Name    | Status | Networks              | Image      | Flavor  |
+--------------------------------------+---------+--------+-----------------------+------------+---------+
| 7a97b87a-12df-46ee-85c2-2229abaa2b15 | test-vm | ACTIVE | test-net=192.168.0.81 | test-image | m1.tiny |
```


## Access Horizon dashboard

Open browser:

```text
http://10.2.0.4
```

Get password:

```console
cat /etc/kolla/passwords.yml | grep keystone_admin_password
```

Login:

* Username: admin
* Domain: Default

## What you've learned

You validated your OpenStack deployment, created networking, uploaded an image, and successfully launched a virtual machine using OpenStack.
