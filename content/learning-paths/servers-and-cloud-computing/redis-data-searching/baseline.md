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
(integer) 10000
```

You can verify by fetching a sample record:

```console
redis-cli GET key:5000
```

You should see an output similar to:

```output
"value-5000"
```

### Perform Basic Data Search Tests
Retrieve a single key:

```console
redis-cli GET key:1234
```

You should see an output similar to:

```output
"value-1234"
```


### Search for Multiple Keys Using Pattern Matching
Use the `KEYS` command to search for keys matching a pattern:

```console
redis-cli KEYS "key:1*"
```
You should see an output similar to:

```output
   1) "key:1392"
   2) "key:1076"
   3) "key:1683"
   4) "key:1490"
   5) "key:117"
   6) "key:1293"
   7) "key:1791"
   8) "key:1891"
   9) "key:1543"
..........
```

### Production-Safe Searching with SCAN
Use the `SCAN` command for larger datasets — it’s non-blocking and iterates safely.

```console
redis-cli SCAN 0 MATCH "key:1*" COUNT 100
```

You should see an output similar to:

```output
1) "9792"
2) 1) "key:151"
   2) "key:1845"
   3) "key:1397"
   4) "key:1501"
   5) "key:1994"
   6) "key:1475"
   7) "key:1522"
   8) "key:1884"
```

Continue scanning until the cursor returns 0 (means iteration complete).

### Measure Data Retrieval Performance
**Time Single Key Lookup**: 

```console
(time redis-cli GET key:9000) 2>&1
```
You should see an output similar to:

```output
"value-9000"

real    0m0.002s
user    0m0.002s
sys     0m0.000s
```


Output example:
