---
title: Apache Flink Benchmarking
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## Apache Flink Benchmarking
This guide provides step-by-step instructions to set up and run **Apache Flink Benchmarks** on a **GCP SUSE VMs**. It covers cloning the repository, building the benchmarks, exploring the JAR, and listing available benchmarks.

### Clone the Repository
Start by cloning the official Flink benchmarks repository. This repository contains all the benchmark definitions and example jobs.

```console
cd ~
git clone https://github.com/apache/flink-benchmarks.git
cd flink-benchmarks
```

### Build the Benchmarks with Maven
Use Maven to compile the benchmarks and generate the benchmark JAR. Skip tests to save time.

```console
mvn clean package -DskipTests
```
- **mvn clean package** → Cleans previous builds and packages the project.

After this step, the target directory will contain the compiled **benchmarks.jar**.

### Explore the JAR Contents
Verify the generated files inside the `target` directory:

```console
cd target
ls
```
You should see an output similar to:

```output
benchmark-0.1.jar  classes            generated-test-sources  maven-status         protoc-plugins
benchmarks.jar     generated-sources  maven-archiver          protoc-dependencies  test-classes
```
- **benchmarks.jar**→ The main benchmark JAR file used to run Flink benchmarks.

### List Available Benchmarks
To view all the benchmarks included in the JAR:

```console
java -jar benchmarks.jar -l
```
- `-l` → Lists all benchmarks packaged in the JAR.
- This helps you identify which benchmarks you want to execute on your VM.

### Run Selected Benchmarks
While the Flink benchmarking project includes multiple suites for state backends, windowing, checkpointing, and scheduler performance, this Learning path focuses on the Remote Channel Throughput benchmark to evaluate network and I/O performance.

**Remote Channel Throughput**: `Remote Channel Throughput` – Measures the data transfer rate between remote channels in Flink, helping to evaluate network and I/O performance.
```console
java -jar benchmarks.jar org.apache.flink.benchmark.RemoteChannelThroughputBenchmark.remoteRebalance
```
You should see an output similar to:
```output

Result "org.apache.flink.benchmark.RemoteChannelThroughputBenchmark.remoteRebalance":
  13457.642 ±(99.9%) 317.853 ops/ms [Average]
  (min, avg, max) = (13136.253, 13457.642, 13729.847), stdev = 210.240
  CI (99.9%): [13139.789, 13775.495] (assumes normal distribution)

# Run complete. Total time: 00:23:29

Benchmark                                          (mode)   Mode  Cnt      Score     Error   Units
RemoteChannelThroughputBenchmark.remoteRebalance  ALIGNED  thrpt   10  16251.289 ± 631.734  ops/ms
RemoteChannelThroughputBenchmark.remoteRebalance  DEBLOAT  thrpt   10  13457.642 ± 317.853  ops/ms
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

| Benchmark                                          | Mode     | Count | Score (ops/ms) | Error (±) | Min        | Max        | Stdev    | CI (99.9%)             | Units  |
|---------------------------------------------------|---------|-------|----------------|-----------|------------|------------|---------|------------------------|--------|
| RemoteChannelThroughputBenchmark.remoteRebalance | ALIGNED | 30    | 24873.046      | 892.673   | 11195.028  | 12425.761  | 421.057 | [11448.649, 12011.275] | ops/ms |
| RemoteChannelThroughputBenchmark.remoteRebalance | DEBLOAT | 30    | 11729.962      | 281.313   | 11195.028  | 12425.761  | 421.057 | [11448.649, 12011.275] | ops/ms |

### Benchmark summary on Arm64
Results from the earlier run on the `c4a-standard-4` (4 vCPU, 16 GB memory) Arm64 VM in GCP (SUSE):

| Benchmark                                          | Mode     | Count | Score (ops/ms) | Error (±) | Min       | Max       | Stdev   | CI (99.9%)             | Units  |
|---------------------------------------------------|---------|-------|----------------|-----------|-----------|-----------|---------|------------------------|--------|
| RemoteChannelThroughputBenchmark.remoteRebalance | ALIGNED | 30    | 17445.341      | 153.256   | 10289.593 | 10687.736 | 89.987  | [10476.390, 10596.633] | ops/ms |
| RemoteChannelThroughputBenchmark.remoteRebalance | DEBLOAT | 30    | 10536.511      | 60.121    | 10289.593 | 10687.736 | 89.987  | [10476.390, 10596.633] | ops/ms |

### Apache Flink performance benchmarking comparison on Arm64 and x86_64

- The **ALIGNED mode** achieved an average throughput of **17,445 ops/ms**, demonstrating higher performance on the Arm64 VM.  
- The **DEBLOAT mode** achieved an average throughput of **10,537 ops/ms**, slightly lower due to optimization differences.  
- The benchmark confirms that the **Arm64 architecture** efficiently handles Flink's remote channel throughput workloads.  
- Overall, the average throughput across both modes is approximately **14,854 ops/ms**, indicating strong baseline performance for Arm64 deployments.
