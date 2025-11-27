---
title: ClickHouse Benchmarking
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


##  ClickHouse Benchmark on GCP SUSE Arm64 VM
ClickHouse provides an official benchmarking utility called **`clickhouse-benchmark`**, which is included **by default** in the ClickHouse installation.
This tool measures **query throughput and latency**.

### Verify the benchmarking tool exists
Run on the VM host terminal:

```console
which clickhouse-benchmark
```

### Prepare benchmark database and table
Run inside the ClickHouse client:

```console
clickhouse client
```

```sql
CREATE DATABASE IF NOT EXISTS bench;
USE bench;

CREATE TABLE IF NOT EXISTS hits
(
    event_time DateTime,
    user_id UInt64,
    url String
)
ENGINE = MergeTree
ORDER BY (event_time, user_id);
```

```output
Query id: 83485bc4-ad93-4dfc-bafe-c0e2a45c1b34
Ok.
0 rows in set. Elapsed: 0.005 sec.
```

Exit client:

```console
exit;
```
### Load benchmark data
Run on VM host

```sql
clickhouse-client --query "
INSERT INTO bench.hits
SELECT
    now() - number,
    number,
    concat('/page/', toString(number % 100))
FROM numbers(1000000)"
```

This inserts 1 million rows.

**Verify:**

```sql
clickhouse-client --query "SELECT count(*) FROM bench.hits"
```

```output
1000000
```

### Read query benchmark (official)
Run on the VM host

```sql
clickhouse-benchmark \
  --host localhost \
  --port 9000 \
  --iterations 10 \
  --concurrency 1 \
  --query "SELECT count(*) FROM bench.hits WHERE url LIKE '/page/%'"
```

```output
Loaded 1 queries.

Queries executed: 10 (100%).

localhost:9000, queries: 10, QPS: 63.167, RPS: 63167346.434, MiB/s: 957.833, result RPS: 63.167, result MiB/s: 0.000.

0%              0.003 sec.
10%             0.003 sec.
20%             0.003 sec.
30%             0.004 sec.
40%             0.004 sec.
50%             0.004 sec.
60%             0.004 sec.
70%             0.004 sec.
80%             0.004 sec.
90%             0.004 sec.
95%             0.005 sec.
99%             0.005 sec.
99.9%           0.005 sec.
99.99%          0.005 sec.
```


### Benchmark aggregation query

```sql
clickhouse-benchmark \
  --host localhost \
  --port 9000 \
  --iterations 10 \
  --concurrency 2 \
  --query "
    SELECT
        url,
        count(*) AS total
    FROM bench.hits
    GROUP BY url
  "
```

```output
Queries executed: 10 (100%).

localhost:9000, queries: 10, QPS: 67.152, RPS: 67151788.647, MiB/s: 1018.251, result RPS: 6715.179, result MiB/s: 0.153.

0%              0.005 sec.
10%             0.005 sec.
20%             0.005 sec.
30%             0.007 sec.
40%             0.007 sec.
50%             0.007 sec.
60%             0.007 sec.
70%             0.007 sec.
80%             0.007 sec.
90%             0.007 sec.
95%             0.008 sec.
99%             0.008 sec.
99.9%           0.008 sec.
99.99%          0.008 sec.
```

### Benchmark concurrent read workload

```sql
clickhouse-benchmark \
  --host localhost \
  --port 9000 \
  --iterations 20 \
  --concurrency 8 \
  --query "
    SELECT count(*)
    FROM bench.hits
    WHERE user_id % 10 = 0
  "
```

```output
Loaded 1 queries.

Queries executed: 20 (100%).

localhost:9000, queries: 20, QPS: 99.723, RPS: 99723096.882, MiB/s: 760.827, result RPS: 99.723, result MiB/s: 0.001.

0%              0.012 sec.
10%             0.012 sec.
20%             0.013 sec.
30%             0.017 sec.
40%             0.020 sec.
50%             0.029 sec.
60%             0.029 sec.
70%             0.038 sec.
80%             0.051 sec.
90%             0.062 sec.
95%             0.063 sec.
99%             0.078 sec.
99.9%           0.078 sec.
99.99%          0.078 sec.
```

### Measuring insert performance (optional)

```sql
clickhouse-benchmark \
  --iterations 5 \
  --concurrency 4 \
  --query "
    INSERT INTO bench.hits
    SELECT
        now(),
        rand64(),
        '/benchmark'
    FROM numbers(500000)
  "
```

```output
Queries executed: 5 (100%).

localhost:9000, queries: 5, QPS: 20.935, RPS: 10467305.309, MiB/s: 79.859, result RPS: 0.000, result MiB/s: 0.000.

0%              0.060 sec.
10%             0.060 sec.
20%             0.060 sec.
30%             0.060 sec.
40%             0.068 sec.
50%             0.068 sec.
60%             0.068 sec.
70%             0.069 sec.
80%             0.069 sec.
90%             0.073 sec.
95%             0.073 sec.
99%             0.073 sec.
99.9%           0.073 sec.
99.99%          0.073 sec.
```



