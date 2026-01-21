---
title: Install and Access Argo CD on Arm64 GKE
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Overview

In this section, you install **Argo CD** on your Arm64 GKE cluster and access it securely from your browser and CLI.

## Install Argo CD
Install Argo CD on the Arm64 GKE cluster using official upstream manifests in the `argocd` namespace.

**Create namespace:**

```bash
kubectl create namespace argocd
```

**Install Argo CD using official manifests:**

```console
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

**Wait for pods:**

```console
kubectl get pods -n argocd -w
```

```output
NAME                                               READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                    1/1     Running   0          3h20m
argocd-applicationset-controller-944684d77-p8vcv   1/1     Running   0          3h20m
argocd-dex-server-6944b95798-hzp2j                 1/1     Running   0          3h20m
argocd-notifications-controller-7f5b87f55b-8v4zh   1/1     Running   0          3h20m
argocd-redis-c98d5794d-ckczm                       1/1     Running   0          3h20m
argocd-repo-server-7f86545bc4-gcqcv                1/1     Running   0          3h20m
argocd-server-685f5fb66f-24w8m                     1/1     Running   0          3h20m
```

## Expose Argo CD (External Browser Access)
Expose the Argo CD server using a Kubernetes LoadBalancer to enable external browser access to the UI.

```console
kubectl patch svc argocd-server -n argocd \
  -p '{"spec": {"type": "LoadBalancer"}}'
```

**Get external IP:**

```console
kubectl get svc argocd-server -n argocd -w
```

```output
NAME            TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
argocd-server   LoadBalancer   34.118.228.71   34.xx.xx.xx   80:30166/TCP,443:30920/TCP   3h22m
```

## Get Admin Password
Retrieve and decode the initial Argo CD admin password stored as a Kubernetes secret.

```console
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d && echo
```

## Access Argo CD UI
Accesse the Argo CD web UI using the external IP to manage and monitor GitOps applications.

Open in browser:

```bash
https://<ARGOCD_EXTERNAL_IP>
```

**Login:**

- Username: admin
- Password: from the previous step

Accept the TLS warning (self-signed cert).

## Install Argo CD CLI (ARM64)
Install the ARM64 Argo CD CLI for command-line GitOps management and automation.

```console
curl -LO https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-arm64
chmod +x argocd-linux-arm64
sudo mv argocd-linux-arm64 /usr/local/bin/argocd
```

**Verify:**

```console
argocd version
```

```output
argocd: v3.2.5+c56f440
  BuildDate: 2026-01-14T16:38:17Z
  GitCommit: c56f4400f22c7e9fe9c5c12b85576b74369fb6b8
  GitTreeState: clean
  GoVersion: go1.25.5
  Compiler: gc
  Platform: linux/arm64
argocd-server: v3.2.5+c56f440
  BuildDate: 2026-01-14T16:13:04Z
  GitCommit: c56f4400f22c7e9fe9c5c12b85576b74369fb6b8
  GitTreeState: clean
  GoVersion: go1.25.5
  Compiler: gc
  Platform: linux/arm64
  Kustomize Version: v5.7.0 2025-06-28T07:00:07Z
  Helm Version: v3.18.4+gd80839c
  Kubectl Version: v0.34.0
  Jsonnet Version: v0.21.0
```

## Login via CLI
Authenticates the Argo CD CLI with the server to enable GitOps operations from the terminal.

```console
argocd login <ARGOCD_EXTERNAL_IP> \
  --username admin \
  --password <PASTE_PASSWORD> \
  --insecure
```

**Verify:**

```console
argocd app list
```

## What You’ve Accomplished

Argo CD is fully installed and accessible via UI and CLI, ready for GitOps-based application deployment.

