---
title: Couchbase  Baseline Testing on Google Axion C4A Arm Virtual Machine
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Couchbase Baseline Testing on GCP SUSE VMs

### Check Required Ports
Couchbase uses the following ports for basic operation:

- Web Console: `8091`  
- Query Service: `8093` (optional for N1QL queries)  
- Data Service: `11210`  

Check if the ports are listening:

```console
sudo ss -tuln | grep -E '8091|11210'
```

```output
tcp   LISTEN 0      128          0.0.0.0:8091       0.0.0.0:*
tcp   LISTEN 0      1024         0.0.0.0:11210      0.0.0.0:*
tcp   LISTEN 0      1024            [::]:11210         [::]:*
```

### Initialize Couchbase Cluster

```console
/opt/couchbase/bin/couchbase-cli cluster-init \
  -c localhost:8091 \
  --cluster-username Administrator \
  --cluster-password password \
  --cluster-name MyCluster \
  --services data,index,query \
  --cluster-ramsize 1024 \
  --cluster-index-ramsize 512
```

```output
SUCCESS: Cluster initialized
```

### Verify Cluster Nodes

```console
/opt/couchbase/bin/couchbase-cli server-list \
  -u Administrator -p password \
  --cluster localhost
```

```output
ns_1@cb.local 127.0.0.1:8091 healthy active
```

### Web UI Access
Ensure the Couchbase service is running and ports **8091** (Web UI) and **11210** (Data) are open. Then access the Couchbase Web UI in your browser at:  

```console
sudo systemctl start couchbase-server
sudo systemctl enable couchbase-server
sudo systemctl status couchbase-server
```
Once the service is running, Couchbase is accessible in your browser at:

```cpp
http://<VM-IP>:8091
```

