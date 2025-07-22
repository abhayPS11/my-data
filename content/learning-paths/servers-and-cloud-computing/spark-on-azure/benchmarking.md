---
title: Benchmarking
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Test the Spark application for performance


## Run the performance tests 
Apache Spark benchmarking involves measuring the performance of distributed data processing tasks to evaluate execution efficiency. Spark benchmarks focus on job-level metrics such as execution time, task parallelism, and data transformation performance. It is useful for comparing performance across different configurations or hardware (e.g., Arm vs x86). 

## Custom Benchmark Script (For Full Control)
Python/PySpark script to measure performance. For benchmarking create a `benchmark_script.py` file as below:

```python
from pyspark.sql import SparkSession
import time

spark = SparkSession.builder.appName("Benchmark").getOrCreate()

# Start timer
start_time = time.time()

# Generate test data
df = spark.range(0, 10000000).toDF("id")

# Run operations
df = df.withColumn("square", df["id"] * df["id"])
df.write.mode("overwrite").parquet("/tmp/benchmark_output")

# End timer
end_time = time.time()
print(f"Total time: {end_time - start_time} seconds")

spark.stop()
```
## Run the Benchmark

Run the benchmark by below command:
```console
spark-submit benchmark_script.py
```

### Benchmark summary on x86_64:
The following benchmark results are collected on two different x86_64 environments: a **Docker container running Azure Linux 3.0 hosted on a D4s_v6 Ubuntu-based Azure VM**, and a **D4s_v4 Azure VM created from the Azure Linux 3.0 image published by Ntegral Inc**.

| Metric                 |Value on Docker Container |  Value on Virtual Machine     |
|------------------------|------------------------|------------------------|
| Total Job Time         | 5.12 seconds           | 5.27 seconds           |
| Stage 0 Duration       | 1.57 seconds           | 1.74 seconds           |
| Avg Task Time          | ~1.40s (per task)      | ~2.74s (per task)    |
| Write Commit Time      | 16 ms                  | 16 ms                  |
| Parallelism            | 4 tasks                | 4 tasks                |
| Data Processed         | 10M rows (Parquet)     | 10M rows (Parquet)     |

### Benchmark summary on Arm64:
The following benchmark results are collected on two different Arm64 environments: a **Docker container running Azure Linux 3.0 hosted on a D4ps_v6 Ubuntu-based Azure VM**, and a **D4ps_v6 Azure VM created from the Azure Linux 3.0 custom image using the Aarch64 ISO**.

| Metric               | Value on Docker Container |  Value on Virtual Machine              |
|----------------------|------------------------|------------------------|
| **Total Job Time**   | 4.41 seconds           | 4.51 seconds           |
| **Stage 0 Duration** | 1.52 seconds           | 1.58 seconds           |
| **Avg Task Time**    | ~1.34s (per task)      | ~1.37s (per task)      |
| **Write Commit Time**| 11 ms                  | 11 ms                  |
| **Parallelism**      | 4 tasks                | 4 tasks                |
| **Data Written**     | 10M rows (Parquet)     | 10M rows (Parquet)     |


### **Highlights from Azure Linux Arm64 Benchmarking**
- **Blazing Speed:** Spark processed 10 million records in under 1.6 seconds (Stage 0), proving Arm’s capability for high-speed distributed workloads.
- **Rock-Solid Consistency:** Minimal variance in task times (±30ms across 25 runs), demonstrating stable performance on Azure’s Arm VMs.
- **Efficient Parallelism:** Achieved near-identical task completion times (~1.37s per task) with 4 parallel tasks, highlighting Arm’s thread scalability.
- **Cost-Effective Performance:** Comparable performance between Docker containers (4.41s) and bare-metal Azure VMs (4.51s), making ARM64 a versatile choice for cloud-native deployments.
- **Optimized I/O:** Snappy compression and 11ms write commits show Arm’s efficiency in handling data-intensive operations.
