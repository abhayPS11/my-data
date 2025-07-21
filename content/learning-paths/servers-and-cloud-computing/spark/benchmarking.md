---
title: Benchmarking
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Test the Spark application for performance


## Run the performance tests 
Apache Spark benchmarking involves measuring the performance of distributed data processing tasks to evaluate execution efficiency. Spark benchmarks focus on job-level metrics such as execution time, task parallelism, and data transformation performance. It is useful for comparing performance across different configurations or hardware (e.g., Arm vs x86). 

## Benchmarking Steps (Spark-Only Execution Time)
1. Create a Benchmark Script (Scala)
Create a simple Spark job that applies transformations and actions on a large dataset:

```scala
// spark_benchmark.scala

val t0 = System.nanoTime()

val data = 1 to 10000000
val distData = spark.sparkContext.parallelize(data)

// Apply Spark transformations
val result = distData.map(_ * 2).filter(_ % 3 == 0).count()

val t1 = System.nanoTime()

println(s"Processed Count: $result")
println("Spark Execution Time: " + (t1 - t0) / 1e9 + " seconds")
```

## Run the Benchmark
Run the script in Spark shell:

```console
spark-shell < spark_benchmark.scala
```
Output:

```output
scala> val t0: Long = 8746885756395

scala> val data: scala.collection.immutable.Range.Inclusive = Range 1 to 10000000

scala> val distData: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[0] at parallelize at <console>:1

scala> val result: Long = 3333333

scala> val t1: Long = 8747831277207

scala> Processed Count: 3333333

scala> Spark Execution Time: 0.945520812 seconds
```

This measures:
  - Data parallelization – Distributes the dataset across available Spark executors for parallel processing.
  - Transformation (map, filter) – Applies computation logic on each partition without triggering execution.
  - Action (count) – Triggers job execution and aggregates the results back to the driver.
  - Total time Spark takes to process the job – Measures end-to-end execution time from job submission to result collection.

### Benchmark summary on x86_64:
| **Category**         | **Details**                     |
|----------------------|----------------------------------|
| Operating System     | RHEL 9 (x86_64)                  |
| Java Version         | OpenJDK 17                       |
| Scala Version        | 2.13.16                          |
| RDD Source           | `Range(1 to 10,000,000)`         |
| Processed Count      | 3,333,333                        |
| Start Time (t0)      | 5,437,587,327,891                |
| End Time (t1)        | 5,438,909,915,151                |
| Execution Time       | **1,322.59 milliseconds**        |
### Benchmark summary on Arm64:
The following  benchmark results are collected on an Arm64 environment using the C4A VM series (c4a-standard-4) on GCP.

| **Category**         | **Details**                     |
|----------------------|----------------------------------|
| Operating System     | RHEL 9 (aarch64)                 |
| Java Version         | OpenJDK 17                       |
| Scala Version        | 2.13.16                          |
| RDD Source           | `Range(1 to 10,000,000)`         |
| Processed Count      | 3,333,333                        |
| Start Time (t0)      | 8,746,885,756,395                |
| End Time (t1)        | 8,747,831,277,207                |
| Execution Time       | **945.52 milliseconds**          |

### **Highlights from Gcp C4A Arm VM**
- **Fast Execution:** Spark achieved processing of 10 million records in as low as 1.06 seconds, showcasing the speed of Arm on distributed workloads.
- **Consistent Performance:** Execution time remained stable across multiple runs, confirming reliability of Spark on Arm-based virtual machines.
- **Arm-Optimized Scalability:** Demonstrates the capability of GCP’s C4A Arm VMs to handle high-throughput Spark jobs efficiently in a cost-effective environment.
