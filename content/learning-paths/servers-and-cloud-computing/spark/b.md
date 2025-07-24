---
title: Benchmarking
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Apache Spark Internal Benchmarking (v4.0.0)
Apache Spark includes internal micro-benchmarks to evaluate the performance of core components like SQL execution, aggregation, joins, and data source reads. These benchmarks are helpful for comparing platforms such as x86_64 vs Arm64.
Below are the steps to run Spark’s built-in SQL benchmarks using the SBT-based framework.

Clone the Apache Spark source code
```console
$ git clone https://github.com/apache/spark.git
```
Downloads the full Spark source including internal test suites and benchmarking tools.

Checkout the desired Spark version
```console
$ cd spark/ && git checkout v4.0.0
```
Switches to the stable Spark 4.0.0 release, which supports the latest internal benchmarking APIs.

Build Spark with benchmarking profile enabled
```console
$ ./build/sbt -Pbenchmarks clean package
```
Compiles Spark and its dependencies, enabling the benchmarks build profile for performance testing.

Run a built-in benchmark suite
```console
$ ./build/sbt -Pbenchmarks "sql/test:runMain org.apache.spark.sql.execution.benchmark.AggregateBenchmark"
```  
Executes the AggregateBenchmark, which compares performance of SQL aggregation operations (e.g., SUM, STDDEV) with and without WholeStageCodegen.

Output:
```output
ing builtin-java classes where applicable
[info] Running benchmark: agg w/o group
[info]   Running case: agg w/o group wholestage off
```
### Benchmark summary on x86_64:
The following  benchmark results are collected on an x86_64 environment.
### Spark `AggregateBenchmark` Results — x86 GCP VM

| Benchmark Name                 | Best Time (ms) | Per Row (ns) | Relative |
| ------------------------------ | -------------- | ------------ | -------- |
| UnsafeRowhash                  | 203            | 9.7          | 1.0×     |
| murmur3 hash                   | 68             | 3.3          | 3.0×     |
| fast hash                      | 41             | 2.0          | 4.9×     |
| arrayEqual                     | 146            | 6.9          | 1.4×     |
| Java HashMap (Long)            | 68             | 3.2          | 3.0×     |
| Java HashMap (two ints)        | 90             | 4.3          | 2.2×     |
| Java HashMap (UnsafeRow)       | 570            | 27.2         | 0.4×     |
| LongToUnsafeRowMap (opt=false) | 368            | 17.5         | 0.6×     |
| LongToUnsafeRowMap (opt=true)  | 75             | 3.6          | 2.7×     |
| BytesToBytesMap (off Heap)     | 626            | 29.9         | 0.3×     |
| BytesToBytesMap (on Heap)      | 636            | 30.3         | 0.3×     |
| **Aggregate HashMap**          | **31**         | **1.5**      | **6.6×** |

### Benchmark summary on Arm64:
The following  benchmark results are collected on an Arm64 environment using the C4A virtula machine series (c4a-standard-4) on GCP.

| Benchmark Name                 | Best Time (ms) | Per Row (ns) | Relative |
| ------------------------------ | -------------- | ------------ | -------- |
| UnsafeRowhash                  | 137            | 6.5          | 1.0×     |
| murmur3 hash                   | 48             | 2.3          | 2.9×     |
| fast hash                      | 42             | 2.0          | 3.3×     |
| arrayEqual                     | 71             | 3.4          | 1.9×     |
| Java HashMap (Long)            | 269            | 12.8         | 0.5×     |
| Java HashMap (two ints)        | 273            | 13.0         | 0.5×     |
| Java HashMap (UnsafeRow)       | 507            | 24.2         | 0.3×     |
| LongToUnsafeRowMap (opt=false) | 237            | 11.3         | 0.6×     |
| LongToUnsafeRowMap (opt=true)  | 76             | 3.6          | 1.8×     |
| BytesToBytesMap (off Heap)     | 472            | 22.5         | 0.3×     |
| BytesToBytesMap (on Heap)      | 505            | 24.1         | 0.3×     |
| **Aggregate HashMap**          | **23**         | **1.1**      | **5.9×** |

### **Highlights from Gcp C4A Arm VM**

- **Aggregate HashMap**achieved the fastest performance with **1.1 ns** per row, showing **5.9×** speedup over the baseline.
- **Fast hash** and **murmur3 hash** performed well with **2.0 ns** and **2.3 ns** per row respectively, maintaining over **2.9×** relative performance.
- Java-based hash maps (e.g., `Java HashMap (Long))` showed **lower efficiency** with per-row times exceeding **12 ns**, indicating GC or memory overheads on Arm.
