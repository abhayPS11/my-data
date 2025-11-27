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
  --host=localhost \
  --port=9000 \
  --repetitions=10 \
  --concurrency=1 \
  --query="SELECT count(*) FROM bench.hits WHERE url LIKE '/page/%'"
```

### Benchmark aggregation query

```sql
clickhouse-benchmark \
  --host=localhost \
  --port=9000 \
  --repetitions=10 \
  --concurrency=2 \
  --query="
    SELECT
        url,
        count(*) AS total
    FROM bench.hits
    GROUP BY url
  "
```

### Benchmark concurrent read workload

```sql
clickhouse-benchmark \
  --host=localhost \
  --port=9000 \
  --repetitions=20 \
  --concurrency=8 \
  --query="
    SELECT
        count(*)
    FROM bench.hits
    WHERE user_id % 10 = 0
  "
```

### Measuring insert performance (optional)

```sql
clickhouse-benchmark \
  --repetitions=5 \
  --concurrency=4 \
  --query="
    INSERT INTO bench.hits
    SELECT
        now(),
        rand(),
        '/benchmark'
    FROM numbers(500000)
  "
```



