---
title: Deploy Applications using GitOps with Argo CD
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Overview

In this section, you deploy a **production-ready application** using **pure GitOps** with Argo CD on an Arm64 GKE cluster.

## Prerequisite

1. You must have **your own GitHub repository** (an empty repository is sufficient) to store GitOps manifests.
2. Create the repository **on GitHub first** (via UI or CLI). For example: `argocd-arm-gitops`.

## Create GitOps Repository

```console
mkdir -p argocd-arm-gitops/apps/nginx
cd argocd-arm-gitops
git init
```
This initializes a local Git repository that will later be pushed to your own GitHub repository.

**Repository structure:**

```text
Copy code
argocd-arm-gitops/
└── apps/
    └── nginx/
        ├── namespace.yaml
        ├── deployment.yaml
        └── service.yaml
```

## Kubernetes Manifests

**`namespace.yaml`**

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: prod
```

**deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  namespace: prod
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80
```

**service.yaml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  namespace: prod
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
```

## Commit and Push

```console
git add .
git commit -m "Initial ARM GitOps app"
git branch -M main
git remote add origin https://github.com/<YOUR_GITHUB_USERNAME>/argocd-arm-gitops.git
git push -u origin main
```
Replace <YOUR_GITHUB_USERNAME> with your own GitHub username or organization name.

## Register Application in Argo CD

Create **argo-app.yaml**:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-prod
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/<YOUR_ORG>/argocd-arm-gitops.git
    targetRevision: main
    path: apps/nginx
  destination:
    server: https://kubernetes.default.svc
    namespace: prod
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```
Ensure the repoURL points to your own GitHub repository.

**Apply:**

```console
kubectl apply -f argo-app.yaml
```

## Verify GitOps Deployment

```console
kubectl get pods -n prod
kubectl get svc -n prod
```

Access application:

```bash
curl http://<NGINX_EXTERNAL_IP>
```

## Test Self-Healing

```console
kubectl scale deployment nginx -n prod --replicas=1
```

Argo CD automatically restores the desired state.

## Learning Outcome — Achieved

- ARM64 GKE cluster
- Argo CD GitOps workflow
- Automated sync & self-healing
