---

title: Deploy OpenStack on Azure ARM using DevStack (Cobalt 100)
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Deploy OpenStack on ARM using DevStack (Azure Cobalt 100)

This guide walks you through deploying OpenStack using DevStack on an ARM-based Azure VM.

You will configure a fully working OpenStack environment with access to the Horizon web UI.

---

## Objective

By the end of this guide, you will:

* Deploy OpenStack (DevStack) on ARM
* Fix ARM-specific issues (etcd, libvirt, networking)
* Access Horizon via public IP
* Validate services using CLI

---

## Environment

| Component | Value                |
| --------- | -------------------- |
| Platform  | Azure Cobalt (ARM64) |
| OS        | Ubuntu 24.04         |
| VM Size   | D4ps_v6              |
| RAM       | ≥ 8 GB               |
| Disk      | ≥ 80 GB              |

---

## Clean setup

```console id="clean01"
sudo rm -rf ~/devstack
sudo rm -rf /opt/stack
sudo rm -rf /var/lib/etcd
sudo rm -f /etc/systemd/system/etcd.service
```

---

## System preparation

```console id="prep01"
sudo apt update && sudo apt upgrade -y

sudo apt install -y \
git curl vim net-tools python3-pip
```

---

## Set hostname

```console id="host01"
sudo hostnamectl set-hostname openstack-arm
exec bash
```

---

## Install etcd (ARM fix)

Download and install etcd:

```console id="etcd01"
cd /tmp
wget https://github.com/etcd-io/etcd/releases/download/v3.5.13/etcd-v3.5.13-linux-arm64.tar.gz
tar -xvf etcd-v3.5.13-linux-arm64.tar.gz
cd etcd-v3.5.13-linux-arm64
sudo cp etcd etcdctl /usr/local/bin/
```

---

## Configure etcd service

```console id="etcd02"
sudo vi /etc/systemd/system/etcd.service
```

```ini id="etcdconf"
[Unit]
Description=etcd
After=network.target

[Service]
User=azureuser
ExecStart=/usr/local/bin/etcd \
  --data-dir=/var/lib/etcd
Restart=always

[Install]
WantedBy=multi-user.target
```

---

## Start etcd

```console id="etcd03"
sudo mkdir -p /var/lib/etcd
sudo chown -R $USER:$USER /var/lib/etcd

sudo systemctl daemon-reload
sudo systemctl enable etcd
sudo systemctl start etcd
```

---

## Install DevStack

```console id="dev01"
cd ~
git clone https://opendev.org/openstack/devstack
cd devstack
```

---

## Configure DevStack

```console id="dev02"
vi local.conf
```

### Final working configuration (ARM optimized)

```ini id="localconf"
[[local|localrc]]
ADMIN_PASSWORD=admin
DATABASE_PASSWORD=admin
RABBIT_PASSWORD=admin
SERVICE_PASSWORD=admin

HOST_IP=10.3.0.4
SERVICE_HOST=10.3.0.4

enable_service horizon

disable_service neutron
disable_service q-agt
disable_service q-dhcp
disable_service q-l3
disable_service q-meta
disable_service q-svc
disable_service ovn-controller
disable_service ovs-vswitchd
disable_service ovsdb-server

disable_service etcd3

KEYSTONE_USE_MOD_WSGI=False
ENABLE_HTTPD_MOD_WSGI_SERVICES=False

LIBVIRT_TYPE=qemu

disable_service tempest
```

👉 Replace IP:

```console id="ipcheck"
hostname -I
```

---

## Deploy OpenStack

```console id="deploy01"
./stack.sh | tee stack.log
```

⏳ Deployment time: ~15–25 minutes

---

## Access Horizon dashboard

Open in browser:

```text id="horizonurl"
http://<PUBLIC_IP>/dashboard
```

Example:

```text id="exampleurl"
http://4.186.31.18/dashboard
```

---

## Login credentials

```text id="logininfo"
Username: admin
Password: admin
```

---

## Azure network fix (critical)

Ensure port 80 is open:

```text id="azurefix"
Azure Portal → VM → Networking → Inbound Rules
```

| Port | Protocol | Action |
| ---- | -------- | ------ |
| 80   | TCP      | Allow  |

---

## Verify via CLI

```console id="verify01"
source openrc admin admin

openstack service list
openstack compute service list
```

---

## Expected output

Services:

```text id="servicesok"
Keystone → OK
Nova → OK
Glance → OK
Placement → OK
Cinder → OK
```

Compute:

```text id="computeok"
nova-scheduler → up
nova-conductor → up
nova-compute → up
```

---

## What you've learned

You successfully deployed OpenStack using DevStack on an ARM-based Azure VM.

You resolved ARM-specific issues including:

* etcd compatibility
* networking limitations
* libvirt virtualization (QEMU)

You validated the deployment using CLI and accessed the Horizon UI.

---
