---
title: Install and Run Qdrant
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

# Install and Run Qdrant

This section explains how to install Docker and deploy the **Qdrant vector database** on an Axion Arm virtual machine.


## Update the System

```bash
sudo zypper refresh
sudo zypper update -y
```

## Install Required Packages

```bash
sudo zypper install -y docker python3 python3-pip git
sudo zypper install -y python311 python311-pip
```

## Enable Docker

```bash
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

## Verify installation:

```bash
docker --version
```

You should see an output similar to:

## Run Qdrant

```bash
docker run -d \
-p 6333:6333 \
-p 6334:6334 \
-v $(pwd)/qdrant_storage:/qdrant/storage \
qdrant/qdrant
```

You should see an output similar to:
```output
latest: Pulling from qdrant/qdrant
3ea009573b47: Pull complete
4f4fb700ef54: Pull complete
ea8055cf6833: Pull complete
9d7bb093ff98: Pull complete
13053c6d0c21: Pull complete
c017fa517b2b: Pull complete
3e2c95baf78f: Pull complete
b940a5cd37f5: Pull complete
Digest: sha256:f1c7272cdac52b38c1a0e89313922d940ba50afd90d593a1605dbbc214e66ffb
Status: Downloaded newer image for qdrant/qdrant:latest
1af9f6ac9cef017016837667f68aeed22a74f0f6352effd568dfa188337820c0
```

## Verify Qdrant

```bash
docker ps
```

You should see an output similar to:
```output
1af9f6ac9cef   qdrant/qdrant   "./entrypoint.sh"   13 seconds ago   Up 11 seconds   0.0.0.0:6333-6334->6333-6334/tcp, [::]:6333-6334>6333-6334/tcp   inspiring_dijkstra
```

## Test API

```bash
curl http://localhost:6333
```

You should see an output similar to:
```output
{"title":"qdrant - vector search engine","version":"1.17.0","commit":"4ab6d2ee0f6c718667e553b1055f3e944fef025f"}gcpuser@qdrant-arm64~>
```
