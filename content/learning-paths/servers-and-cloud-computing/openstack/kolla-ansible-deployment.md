---
title: Deploy OpenStack using Kolla-Ansible on Azure Ubuntu Arm64 VM
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Deploy OpenStack on Azure Ubuntu 24.04 Arm64 using Kolla-Ansible

This guide walks you through deploying OpenStack using Kolla-Ansible on an Azure Ubuntu 24.04 Arm64 virtual machine.

The deployment is fully containerized and optimized for ARM64 (aarch64).


## Prerequisites

* Ubuntu 24.04 ARM64 VM (Azure)
* Minimum 4 vCPU, 8 GB RAM (16 GB recommended)
* Disk: 100 GB+
* Two network interfaces:

  * `eth0` → management (with IP)
  * `eth1` → external (no IP)

## Configure external interface

```console
sudo ip addr flush dev eth1
sudo ip link set eth1 up
```

## Install system dependencies

```console
sudo apt update

sudo apt install -y \
python3-venv python3-dev python3-pip \
gcc libffi-dev libssl-dev \
libdbus-1-dev libglib2.0-dev pkg-config \
meson ninja-build curl
```

## Fix and install Docker

```console
## Fix and install Docker

```console
sudo apt update
sudo apt install -y docker.io

sudo systemctl enable docker
sudo systemctl start docker

sudo usermod -aG docker $USER

# Apply group without logout
newgrp docker
```

## Create Python virtual environment

```console
python3 -m venv ~/kolla-venv
source ~/kolla-venv/bin/activate
```


## Install Kolla-Ansible and dependencies

```console
pip install -U pip

pip install \
'ansible-core>=2.15,<2.17' \
kolla-ansible \
docker \
dbus-python
ansible-galaxy collection install openstack.kolla
kolla-ansible install-deps
```

## Configure Kolla

```console
sudo mkdir -p /etc/kolla
sudo chown $USER:$USER /etc/kolla

cp -r ~/kolla-venv/share/kolla-ansible/etc_examples/kolla/* /etc/kolla
cp ~/kolla-venv/share/kolla-ansible/ansible/inventory/all-in-one .
```


## Edit globals.yml

```console
vi /etc/kolla/globals.yml
```

```yaml
kolla_base_distro: "debian"
openstack_tag_suffix: "-aarch64"

network_interface: "eth0"
neutron_external_interface: "eth1"

kolla_internal_vip_address: "10.2.0.4"

enable_keepalived: "no"
```

## Configure Nova (QEMU for ARM)

```console
sudo mkdir -p /etc/kolla/config

cat <<EOF | sudo tee /etc/kolla/config/nova.conf
[libvirt]
virt_type = qemu
cpu_mode = none
EOF
```


## Generate passwords

```console
kolla-genpwd
```

---

## Deploy OpenStack

```console
kolla-ansible bootstrap-servers -i all-in-one
```

```output
TASK [openstack.kolla.baremetal : Set https proxy for git] ***************************************************************************
skipping: [localhost]

TASK [openstack.kolla.baremetal : Set http proxy for git] ****************************************************************************
skipping: [localhost]

TASK [openstack.kolla.baremetal : Configure ceph for zun] ****************************************************************************
skipping: [localhost]

PLAY RECAP ***************************************************************************************************************************
localhost                  : ok=41   changed=13   unreachable=0    failed=0    skipped=30   rescued=0    ignored=0
```

```
kolla-ansible prechecks -i all-in-one 
```

```output
PLAY [Apply role venus] **************************************************************************************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: enable_skyline_True

PLAY [Apply role skyline] ************************************************************************************************************
skipping: no hosts matched

PLAY RECAP ***************************************************************************************************************************
localhost                  : ok=96   changed=0    unreachable=0    failed=0    skipped=142  rescued=0    ignored=0
```

```console
kolla-ansible pull -i all-in-one
```

```output
PLAY [Apply role skyline] ************************************************************************************************************
skipping: no hosts matched

PLAY RECAP ***************************************************************************************************************************
localhost                  : ok=33   changed=14   unreachable=0    failed=0    skipped=52   rescued=0    ignored=0
```
```console
kolla-ansible deploy -i all-in-one deploy
````

```output
PLAY [Apply role skyline] ************************************************************************************************************
skipping: no hosts matched

PLAY RECAP ***************************************************************************************************************************
localhost                  : ok=368  changed=34   unreachable=0    failed=0    skipped=267  rescued=0    ignored=0
```

```
kolla-ansible -i all-in-one post-deploy
```

## Load environment

```console
source /etc/kolla/admin-openrc.sh
```

## Verify services

```console
openstack compute service list
openstack network agent list
```

All services should be UP.

```output
openstack network agent list
+--------------------------------------+----------------+--------------+----------+---------+-------+----------------------------+
| ID                                   | Binary         | Host         | Zone     | Status  | State | Updated At                 |
+--------------------------------------+----------------+--------------+----------+---------+-------+----------------------------+
| bcb60746-4219-44a7-b850-5f9b28ec12d7 | nova-scheduler | ansible-d8ps | internal | enabled | up    | 2026-04-14T07:38:13.000000 |
| 2054f4ad-2492-46e4-990a-4fdf7d40e20f | nova-conductor | ansible-d8ps | internal | enabled | up    | 2026-04-14T07:38:11.000000 |
| b0d28456-483b-4a9c-bcaf-5932364e32b6 | nova-compute   | ansible-d8ps | nova     | enabled | up    | 2026-04-14T07:38:16.000000 |
+--------------------------------------+----------------+--------------+----------+---------+-------+----------------------------+
+------------------------+--------------------+--------------+-------------------+-------+-------+--------------------------+
| ID                     | Agent Type         | Host         | Availability Zone | Alive | State | Binary                   |
+------------------------+--------------------+--------------+-------------------+-------+-------+--------------------------+
| 0ac15427-97e0-4690-    | Metadata agent     | ansible-d8ps | None              | :-)   | UP    | neutron-metadata-agent   |
| be2b-ac3b236a331f      |                    |              |                   |       |       |                          |
| 41dc2dee-106e-4a99-    | DHCP agent         | ansible-d8ps | nova              | :-)   | UP    | neutron-dhcp-agent       |
| 8e7b-94b8fac38fe1      |                    |              |                   |       |       |                          |
| f36a8827-ee1f-4387-    | L3 agent           | ansible-d8ps | nova              | :-)   | UP    | neutron-l3-agent         |
| 9c11-bdbb0c10dee4      |                    |              |                   |       |       |                          |
| fb0f81ce-06e1-4ded-    | Open vSwitch agent | ansible-d8ps | None              | :-)   | UP    | neutron-openvswitch-     |
| b736-1bd329a3a59e      |                    |              |                   |       |       | agent                    |
+------------------------+--------------------+--------------+-------------------+-------+-------+--------------------------+
```


## What you've learned

You deployed OpenStack using Kolla-Ansible on an ARM-based system with Docker, networking, and compute fully configured.
