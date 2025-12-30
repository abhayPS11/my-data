---
title: ClickHouse Benchmarking on Google Axion (Arm)
weight: 7

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## ClickHouse Benchmarking on Axion Processors
This phase benchmarks **query latency on ClickHouse running on Google Axion (Arm64)**.  
The goal is to measure **repeatable query latency** with a focus on **p95 latency**, using data ingested via the real-time Dataflow pipeline.



## Prepare ClickHouse for Accurate Latency Measurement

### Disable Query Cache (Important)
To ensure accurate and repeatable latency measurements, disable the query cache.

Run this **inside the ClickHouse client**:

```sql
SET use_query_cache = 0;
```

---
title: ClickHouse Benchmarking on Google Axion (Arm)
weight: 7

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Phase 4 – ClickHouse Benchmarking on Axion Processors

This phase benchmarks **query latency on ClickHouse running on Google Axion (Arm64)**.  
The goal is to measure **repeatable query latency** with a focus on **p95 latency**, using data ingested via the real-time Dataflow pipeline.

> ⚠️ This phase assumes:
> - ClickHouse is installed and running (Phase 2)
> - Real-time data ingestion via Dataflow is working (Phase 3)
> - All benchmarking is performed **only on Axion processors**

---

## Phase 4.1 – Prepare ClickHouse for Accurate Latency Measurement

### Disable Query Cache (Important)

To ensure accurate and repeatable latency measurements, disable the query cache.

Run this **inside the ClickHouse client**:

```sql
SET use_query_cache = 0;
```
This ensures every query is executed fully and not served from cache.

### Validate Dataset Size
Confirm that the real-time table contains sufficient data:


```console
SELECT count(*) FROM realtime.logs;
```

Expected output:
```console
┌─count()─┐
│  100000 │
└─────────┘
```

If data volume is low, generate additional rows (optional):

```sql
INSERT INTO realtime.logs
SELECT
    now() - number,
    concat('service-', toString(number % 10)),
    'INFO',
    'benchmark message'
FROM numbers(1000000);
```

### Define Benchmark Queries
The following queries represent typical real-time analytics workloads.

**Query 1 – Filtered Count (Service-level analytics)**

```sql
SELECT count(*)
FROM realtime.logs
WHERE service = 'service-5';
```

**Query 2 – Time-windowed Count (Recent activity)**

```sql
SELECT count(*)
FROM realtime.logs
WHERE event_time >= now() - INTERVAL 10 MINUTE;
```

**Query 3 – Aggregation by Service**

```sql
SELECT
    service,
    count(*) AS total
FROM realtime.logs
GROUP BY service
ORDER BY total DESC;
```

### Run Repeatable Latency Measurements
Run each query multiple times using clickhouse-client --time.

Example (run 10 times):

```sql
clickhouse-client --time --query "
SELECT count(*)
FROM realtime.logs
WHERE service = 'service-5';
"
```

Sample output:

250000
0.009


Repeat the same command at least 10 times and record the execution time.

### Collect Latency Samples

Example collected latencies (seconds):

0.009
0.010
0.009
0.007
0.010
0.009
0.018
0.009
0.010
0.008


Store results in a file for analysis:

vi latency-results.txt

Phase 4.6 – Calculate p95 Latency
Sort the values
sort -n latency-results.txt

Calculate p95 manually

For 10 samples:

p95 index = ceil(0.95 × 10) = 10


The 10th value is the p95 latency.

Example:

p95 latency ≈ 0.018 seconds (18 ms)
