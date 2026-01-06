---
title: NGINX Deployment Using Custom Helm Chart
weight: 8

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## NGINX Deployment Using Custom Helm Chart
This document explains how to deploy **NGINX** as a frontend using Helm.

## Goal

Deploy NGINX with:
- Helm
- LoadBalancer service
- External access

### Create Helm Chart

```console
helm create my-nginx
```

### values.yaml

```yaml
image:
  repository: nginx
  tag: latest

service:
  type: LoadBalancer
  port: 80
```

### Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "my-nginx.fullname" . }}

spec:
  replicas: 1
  selector:
    matchLabels:
      app: {{ include "my-nginx.name" . }}

  template:
    metadata:
      labels:
        app: {{ include "my-nginx.name" . }}

    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

### Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "my-nginx.fullname" . }}
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: {{ include "my-nginx.name" . }}
```

### Install & Access

```console
helm install nginx ./my-nginx
kubectl get svc
```

```output
NAME: nginx
LAST DEPLOYED: Tue Jan  6 07:55:52 2026
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch its status by running 'kubectl get --namespace default svc -w nginx-my-nginx'
  export SERVICE_IP=$(kubectl get svc --namespace default nginx-my-nginx --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
  echo http://$SERVICE_IP:80

> kubectl get svc
NAME                       TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes                 ClusterIP      34.118.224.1     <none>        443/TCP        3h21m
nginx-my-nginx             LoadBalancer   34.118.239.19    <pending>     80:31501/TCP   13s
postgres-app-my-postgres   ClusterIP      34.118.225.2     <none>        5432/TCP       12m
redis-my-redis             ClusterIP      34.118.234.155   <none>        6379/TCP       6m14s
```

### Open in browser:

```bash
http://<EXTERNAL-IP>    
```

### Outcome

- NGINX publicly accessible
- Ready to connect to backend services
