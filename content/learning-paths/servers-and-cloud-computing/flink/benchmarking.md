---
title: Apache Flink Benchmarking
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## Apache Flink Benchmarking

This guide shows how to set up and run the Apache Flink Benchmarks on a GCP SUSE VM.

### Clone the Repository

```console
cd ~
git clone https://github.com/apache/flink-benchmarks.git
cd flink-benchmarks
```

### Build the Benchmarks with Maven

```console
mvn clean package -DskipTests
```
This generates the benchmark JAR file in the `targe`t directory (`benchmarks.jar`).

### Explore the JAR Contents

```console
cd target
ls
```
You should see an output similar to:

```output
benchmark-0.1.jar  classes            generated-test-sources  maven-status         protoc-plugins
benchmarks.jar     generated-sources  maven-archiver          protoc-dependencies  test-classes
```

### List Available Benchmarks

To see all benchmarks inside the JAR:

```console
java -jar benchmarks.jar -l
```

### Run Selected Benchmarks

**Remote Channel Throughput**: `Remote Channel Throughput` – Measures the data transfer rate between remote channels in Flink, helping to evaluate network and I/O performance.
```console
java -jar benchmarks.jar org.apache.flink.benchmark.RemoteChannelThroughputBenchmark.remoteRebalance -f 2 -wi 5 -i 5 -t 4
```

- `java -jar benchmarks.jar` – Executes the benchmark JAR built from the Flink Benchmarks repository.  
- `-f 2` – Number of forks: runs the benchmark in 2 separate JVM processes to avoid JVM optimizations carrying over.  
- `-wi 5` – Warmup iterations: runs 5 iterations before actual measurement to let the JVM "warm up" and optimize performance.  
- `-i 5` – Measurement iterations: runs 5 iterations for collecting benchmark results after warmup.  
- `-t 4` – Number of threads: executes the benchmark using 4 threads in parallel.

You should see an output similar to:
```output

Result "org.apache.flink.benchmark.RemoteChannelThroughputBenchmark.remoteRebalance":
  14959.876 ±(99.9%) 1415.047 ops/ms [Average]
  (min, avg, max) = (13951.873, 14959.876, 16047.016), stdev = 935.966
  CI (99.9%): [13544.829, 16374.923] (assumes normal distribution)

# Run complete. Total time: 00:19:06

Benchmark                                          (mode)   Mode  Cnt      Score      Error   Units
RemoteChannelThroughputBenchmark.remoteRebalance  ALIGNED  thrpt   10  22431.281 ±  351.609  ops/ms
RemoteChannelThroughputBenchmark.remoteRebalance  DEBLOAT  thrpt   10  14959.876 ± 1415.047  ops/ms

```

### Flink Benchmark Metrics Explained  

- **Run Count**: Total benchmark iterations executed, higher count improves reliability.  
- **Average Throughput**: Mean operations per second across all iterations.  
- **Standard Deviation**: Variation from average throughput, smaller means more consistent.  
- **Confidence Interval (99.9%)**: Range where the true average throughput lies with 99.9% certainty.  
- **Min Throughput**: The lowest throughput was observed, and it shows worst-case performance.  
- **Max Throughput**: Highest throughput observed, shows best-case performance.

### Benchmark summary on x86_64
To compare the benchmark results, the following results were collected by running the same benchmark on a `x86 - c4-standard-4` (4 vCPUs, 15 GB Memory) x86_64 VM in GCP, running SUSE:



### Benchmark summary on Arm64
Results from the earlier run on the `c4a-standard-4` (4 vCPU, 16 GB memory) Arm64 VM in GCP (SUSE):

| Benchmark                                           | Mode     | Count | Score       | Error       | Units  | Min       | Max       | Avg       | Std Dev  | 99.9% CI              |
|----------------------------------------------------|---------|-------|------------|------------|--------|-----------|-----------|-----------|----------|----------------------|
| RemoteChannelThroughputBenchmark.remoteRebalance  | ALIGNED | 10    | 22431.281  | ±351.609   | ops/ms | -         | -         | -         | -        | -                    |
| RemoteChannelThroughputBenchmark.remoteRebalance  | DEBLOAT | 10    | 14959.876  | ±1415.047  | ops/ms | 13951.873 | 16047.016 | 14959.876 | 935.966  | [13544.829, 16374.923] |

### Apache Flink performance benchmarking comparison on Arm64 and x86_64


