---
title: Redis Baseline Testing on Google Axion C4A Arm Virtual Machine
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Redis Baseline Testing on GCP SUSE VMs
This section performs baseline testing for Redis running on a GCP SUSE Arm64 VM, focusing on data insertion, retrieval, and search performance.

### Prerequisites
Ensure Redis server is running:

```console
redis-server &
```
Verify connectivity:

```console
redis-cli ping
```

output:

```output
PONG
```

### Insert Sample Data
Use `redis-cli` to insert **10,000 sample key-value pairs**:

```console
for i in $(seq 1 10000); do
  redis-cli SET key:$i "value-$i" > /dev/null
done
```

Check that data is stored:

```console
redis-cli DBSIZE
```

```output
(integer) 10001
```

### Perform Basic Data Search Tests
Retrieve a single key:

```console
redis-cli GET key:5000
```

Expected output:

```output
"value-5000"
```

### Search for Multiple Keys Using Pattern Matching
Use the `KEYS` command to search for keys matching a pattern:

```console
redis-cli KEYS "key:50*"
```
### Search for Multiple Keys Using Pattern Matching

Use the `KEYS` command to search for keys matching a pattern:

```bash
redis-cli KEYS "key:50*"

Step 3: Use SCAN for Efficient Searching

The SCAN command is a safer and faster way to iterate over large datasets.

Example:

redis-cli SCAN 0 MATCH "key:99*"


Output example:
