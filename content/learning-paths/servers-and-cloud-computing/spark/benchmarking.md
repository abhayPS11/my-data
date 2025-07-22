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
Create a file named **spark_benchmark.scala**:

```console
nano spark_benchmark.scala
```
Add the below code:

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
t0: Long = 4976303800211
scala>
scala> val data = 1 to 10000000
data: scala.collection.immutable.Range.Inclusive = Range 1 to 10000000
scala> val distData = spark.sparkContext.parallelize(data)
distData: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[0] at parallelize at <console>:23
scala> // Apply Spark transformations
scala> val result = distData.map(_ * 2).filter(_ % 3 == 0).count()
result: Long = 3333333
scala> val t1 = System.nanoTime()
t1: Long = 4977413471129
scala> println(s"Processed Count: $result")
Processed Count: 3333333
scala> println("Spark Execution Time: " + (t1 - t0) / 1e9 + " seconds")
Spark Execution Time: 1.109670918 seconds
```

This measures:
  - Data parallelization – Distributes the dataset across available Spark executors for parallel processing.
  - Transformation (map, filter) – Applies computation logic on each partition without triggering execution.
  - Action (count) – Triggers job execution and aggregates the results back to the driver.
  - Total time Spark takes to process the job – Measures end-to-end execution time from job submission to result collection.

### Benchmark summary on x86_64:
The following  benchmark results are collected on an x86_64 environment.
| **Category**         | **Details**                             |
|----------------------|------------------------------------------|
| Operating System     | RHEL 9 (x86_64)                          |
| Spark Version        | Spark 3.5.6                              |
| RDD Source           | `1 to 10000000`                          |
| Transformation       | `.map(_ * 2).filter(_ % 3 == 0).count()` |
| Processed Count      | 3,333,333                                |
| Start Time (t0)      | 4959983995185 (nanoseconds)             |
| End Time (t1)        | 4961625140647 (nanoseconds)             |
| Execution Time       | 1.641145462 seconds / 1,641.1 milliseconds |


### Benchmark summary on Arm64:
The following  benchmark results are collected on an Arm64 environment using the C4A VM series (c4a-standard-4) on GCP.

| **Category**         | **Details**                             |
|----------------------|------------------------------------------|
| Operating System     | RHEL 9 (Arm64)                          |
| Spark Version        | Spark 3.5.6                              |
| RDD Source           | `1 to 10000000`                          |
| Transformation       | `.map(_ * 2).filter(_ % 3 == 0).count()` |
| Processed Count      | 3,333,333                                |
| Start Time (t0)      | 4976303800211 (nanoseconds)             |
| End Time (t1)        | 4977413471129 (nanoseconds)             |
| Execution Time       | 1.109670918 seconds / 1,109.7 milliseconds |


### **Highlights from Gcp C4A Arm VM**
- **Efficient Spark Execution:** The job completed in ~1.11 seconds, showing strong processing capability of the Arm64-based VM for distributed workloads.
- **Scalable Parallelism:** With a dataset of 10 million records, Spark efficiently applied map-filter-count operations and delivered consistent output.
- **Optimized for Arm**: Demonstrates how GCP’s C4A Arm VMs are a cost-effective and performant option for Spark batch jobs or ETL workloads on modern Linux distros like RHEL 9.
