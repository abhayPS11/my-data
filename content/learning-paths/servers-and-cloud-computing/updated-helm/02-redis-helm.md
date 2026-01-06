---
title: Redis Deployment Using Custom Helm Chart
weight: 7

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## Redis Deployment Using Custom Helm Chart
This document explains how to deploy **Redis** using a custom Helm chart.

## Goal

Deploy Redis with:
- Helm
- ClusterIP service
- Simple connectivity test

### Create Helm Chart

```console
helm create my-redis
```

### Clean Templates
Delete from `my-redis/templates/`:

- ingress.yaml
- hpa.yaml
- serviceaccount.yaml
- tests/
- NOTES.txt

### values.yaml (Replace)

```yaml
replicaCount: 1

image:
  repository: redis
  tag: "7"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 6379
```

### Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "my-redis.fullname" . }}

spec:
  replicas: 1
  selector:
    matchLabels:
      app: {{ include "my-redis.name" . }}

  template:
    metadata:
      labels:
        app: {{ include "my-redis.name" . }}

    spec:
      containers:
        - name: redis
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: 6379
```

### Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "my-redis.fullname" . }}
spec:
  type: ClusterIP
  ports:
    - port: 6379
  selector:
    app: {{ include "my-redis.name" . }}
```

### Install & Test

```console
helm install redis ./my-redis
kubectl exec -it <redis-pod> -- redis-cli ping
```

```output
NAME                                        READY   STATUS    RESTARTS   AGE
postgres-app-my-postgres-6dbc8759b6-jgpxs   1/1     Running   0          6m38s
redis-my-redis-75c88646fb-6lz8v             1/1     Running   0          13s

> kubectl exec -it redis-my-redis-75c88646fb-6lz8v -- redis-cli ping
PONG
```

### Outcome

- Redis running
- Accessible via service name redis
