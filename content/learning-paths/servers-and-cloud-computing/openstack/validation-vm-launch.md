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

## Open vSwitch bridges

In ARM + Azure environments, OVS bridges may not come up automatically.

This causes:

* VM stuck in `ERROR`
* No networking
* No IP assignment

Run the following commands:

```console
sudo ip link set br-int up
sudo ip link set br-ex up
sudo ip link set br-tun up
```

Verify OVS configuration:

```console
sudo ovs-vsctl show
```

**Expected:**

* `br-int`
* `br-ex`
* `br-tun`

All should be present and active.

## Upload image

```console
wget https://cloud.debian.org/images/cloud/bookworm/latest/debian-12-genericcloud-arm64.qcow2

openstack image create "test-image" \
  --file debian-12-genericcloud-arm64.qcow2 \
  --disk-format qcow2 \
  --container-format bare \
  --public
```

## Verify image upload

```console
openstack image list
```

The output is similar to:

```output
+--------------------------------------+------------+--------+
| ID                                   | Name       | Status |
+--------------------------------------+------------+--------+
| 4537107d-4c52-4537-a3d2-bd5c5a13de5b | test-image | active |
+--------------------------------------+------------+--------+
```

## Create network

```console
openstack network create test-net

openstack subnet create test-subnet \
  --network test-net \
  --subnet-range 192.168.0.0/24
```

## Verify network
```console
openstack network list
```

The output is similar to:

```output
+--------------------------------------+----------+--------------------------------------+
| ID                                   | Name     | Subnets                              |
+--------------------------------------+----------+--------------------------------------+
| 1d8b3cc8-a2d3-476c-86d7-4b9533dbefb2 | test-net | a31916fa-783b-4b25-8acb-8694007b9198 |
+--------------------------------------+----------+--------------------------------------+
```

## Verify subnet

```console
openstack subnet list
```

The output is similar to:

```output
+--------------------------------------+-------------+--------------------------------------+----------------+
| ID                                   | Name        | Network                              | Subnet         |
+--------------------------------------+-------------+--------------------------------------+----------------+
| a31916fa-783b-4b25-8acb-8694007b9198 | test-subnet | 1d8b3cc8-a2d3-476c-86d7-4b9533dbefb2 | 192.168.0.0/24 |
+--------------------------------------+-------------+--------------------------------------+----------------+
```


## Create flavor

```console
openstack flavor create m1.tiny --ram 512 --disk 5 --vcpus 1
```

## Verify flavor

```console
openstack flavor list
```

The output is similar to:

```output
+--------------------------------------+---------+-----+------+-----------+-------+-----------+
| ID                                   | Name    | RAM | Disk | Ephemeral | VCPUs | Is Public |
+--------------------------------------+---------+-----+------+-----------+-------+-----------+
| 20ea160b-bff7-4c2a-be1e-588a44dc699a | m1.tiny | 512 |    5 |         0 |     1 | True      |
+--------------------------------------+---------+-----+------+-----------+-------+-----------+
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

The output is similar to:

```output
+--------------------------------------+---------+--------+------------------------+------------+---------+
| ID                                   | Name    | Status | Networks               | Image      | Flavor  |
+--------------------------------------+---------+--------+------------------------+------------+---------+
| 4f42729c-0635-40e2-9432-ac056e40f781 | test-vm | ACTIVE | test-net=192.168.0.150 | test-image | m1.tiny |
+--------------------------------------+---------+--------+------------------------+------------+---------+
```


## Access Horizon dashboard

Open browser:

```text
http://<VM_PUBLIC_IP>
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
