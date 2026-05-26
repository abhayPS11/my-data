---
title: Deploy Longhorn on Azure Cobalt 100 Arm64 VM
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Deploy Longhorn on Azure Cobalt 100 Arm64 VM

### Overview

In this learning path, you'll deploy Longhorn on an Azure Cobalt 100 Arm64 virtual machine using a single-node Kubernetes cluster powered by K3s.

Longhorn provides distributed block storage for Kubernetes workloads and enables persistent storage for stateful applications running on Arm64 infrastructure.

You will learn how to:

- Install K3s Kubernetes on Azure Cobalt 100
- Install Longhorn on Arm64
- Configure Longhorn for single-node operation
- Create Persistent Volumes (PVC)
- Deploy applications using Longhorn storage
- Validate persistent storage functionality
- Benchmark Longhorn storage performance

### Environment

| Component | Value |
|---|---|
| Cloud Platform | Microsoft Azure |
| VM Size | Standard_D4ps_v6 |
| Architecture | Arm64 |
| Operating System | Ubuntu 24.04 LTS |
| Kubernetes Distribution | K3s |
| Storage Platform | Longhorn |

### Prerequisites

Before starting, ensure:

- Azure subscription is available
- Azure Cobalt 100 Arm64 VM is created
- SSH access to VM is working
- Minimum 64 GB disk available


### Update System Packages

```bash
sudo apt update && sudo apt upgrade -y
```

### Install Required Dependencies

```bash
sudo apt install -y \
open-iscsi \
nfs-common \
curl \
wget \
vim \
git
```

### Enable iSCSI Service

```bash
sudo systemctl enable iscsid
```

```bash
sudo systemctl start iscsid
```

Verify:

```bash
sudo systemctl status iscsid
```

Expected:

```text
active (running)
```

### Install K3s Kubernetes

```bash
curl -sfL https://get.k3s.io | sh -
```

### Verify Kubernetes Installation

```bash
sudo kubectl get nodes
```

Expected:

```text
NAME             STATUS   ROLES                  AGE   VERSION
longhorn-Arm64   Ready    control-plane,master   ...
```

### Configure kubectl access

Create the Kubernetes configuration directory:

```bash
mkdir -p ~/.kube
```

Copy the K3s kubeconfig file:

```bash
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
```

Update ownership permissions:

```bash
sudo chown $USER:$USER ~/.kube/config
```

Set the Kubernetes configuration environment variable:

```bahs
export KUBECONFIG=$HOME/.kube/config
```

Persist the configuration:

```bash
echo 'export KUBECONFIG=$HOME/.kube/config' >> ~/.bashrc
source ~/.bashrc
```

Verify access:

```bash
kubectl get nodes
```

The output is similar to:

```output
NAME             STATUS   ROLES           AGE   VERSION
longhorn-Arm64   Ready    control-plane   5s    v1.35.5+k3s1
```

### Create Longhorn Storage Directory

```bash
sudo mkdir -p /longhorn
```

```bash
sudo chmod 777 /longhorn
```

Verify disk space:

```bash
df -h
```

### Install Longhorn

```bash
kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/v1.10.0/deploy/longhorn.yaml
```

### Verify Longhorn Installation

```bash
kubectl get pods -n longhorn-system
```

Wait until all pods show:

```output
NAME                                                READY   STATUS    RESTARTS   AGE
csi-attacher-65c5dd9586-dplk2                       1/1     Running   0          19s
csi-attacher-65c5dd9586-krglp                       1/1     Running   0          19s
csi-attacher-65c5dd9586-lnt7n                       1/1     Running   0          19s
csi-provisioner-c4f7f9c79-6mgtd                     1/1     Running   0          19s
csi-provisioner-c4f7f9c79-jcfnq                     1/1     Running   0          19s
csi-provisioner-c4f7f9c79-rbnlz                     1/1     Running   0          19s
csi-resizer-d4b7d97c7-d26xx                         1/1     Running   0          19s
csi-resizer-d4b7d97c7-mw44r                         1/1     Running   0          19s
csi-resizer-d4b7d97c7-nvgnf                         1/1     Running   0          19s
csi-snapshotter-5c96f555f9-7cmdb                    1/1     Running   0          19s
csi-snapshotter-5c96f555f9-l8dzk                    1/1     Running   0          19s
csi-snapshotter-5c96f555f9-wkt2g                    1/1     Running   0          19s
engine-image-ei-26bab25d-9w2r2                      1/1     Running   0          72s
instance-manager-949b7e7f84f3ef321c4078941b7dac4e   1/1     Running   0          42s
longhorn-csi-plugin-6xrr6                           2/3     Running   0          19s
longhorn-driver-deployer-5889c569cf-88hwk           1/1     Running   0          94s
longhorn-manager-ptwb5                              2/2     Running   0          94s
longhorn-ui-77cdc466b5-8vlrl                        1/1     Running   0          94s
longhorn-ui-77cdc466b5-dbsx5                        1/1     Running   0          94s
```

### Access Longhorn UI

Run:

```bash
kubectl -n longhorn-system port-forward --address 0.0.0.0 service/longhorn-frontend 8080:80
```

Open browser:

```text
http://<PUBLIC_IP>:8080
```

The Longhorn dashboard should appear as shown below.

![Longhorn UI Dashboard showing the cluster summary, storage schedulable capacity, volume health, and node status on the Azure Cobalt 100 Arm64 virtual machine. Verify that the Kubernetes node is schedulable, Longhorn storage is available, and the dashboard is accessible before proceeding to persistent volume configuration.#center](images/longhorn-ui.png "Longhorn UI Dashboard with storage and node summary")

### Configure Longhorn for Single Node

Inside Longhorn UI:

Go to:

```text
Settings → General
```

Find:

```text
Default Replica Count
```

Change:

```text
3 → 1
```

Click:

```text
Save
```

The configuration page should look similar to the image below.

![Longhorn Settings page showing the Default Replica Count configuration for single-node Kubernetes deployment on Azure Cobalt 100 Arm64 virtual machine. Ensure both V1 and V2 Data Engine replica counts are configured to 1 before creating Persistent Volumes in the single-node Longhorn environment.#center](images/longhorn-replica.png "Longhorn Replica Configuration for Single-Node Kubernetes Cluster")

### Verify StorageClass

```bash
kubectl get storageclass
```

The output is similar to:

```output
NAME                   PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)   rancher.io/local-path   Delete          WaitForFirstConsumer   false                  8m48s
longhorn (default)     driver.longhorn.io      Delete          Immediate              true                   6m29s
longhorn-static        driver.longhorn.io      Delete          Immediate              true                   6m26s
```

### What You've Learned

In this section, you learned how to:

- Install K3s on Azure Cobalt 100 Arm64
- Install Longhorn on Arm64
- Access Longhorn UI
- Configure Longhorn for single-node Kubernetes clusters
- Verify Kubernetes storage integration


### What’s Next

Next, you'll:

- Create Persistent Volumes
- Deploy applications using Longhorn storage
- Validate persistent storage
- Benchmark Longhorn storage performance
