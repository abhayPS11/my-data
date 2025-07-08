---
title: Benchmarking via JMH 
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Test the Java application for Performance 
Now that you’ve built and run the Tomcat-like response, you can use it to test the JVM performance using JMH. You can also use it to test the performance difference between Cobalt 100 instances and other similar D series x86_64 based instances. 

{{% notice Note %}}
Before testing, note that you have two options to deploy your Java application on Azure Linux:
{{% Note %}}

#### 1. Using Azure Linux Container (Arm64)

Run your Java application inside a container built on the official Azure Linux Arm64 base image.

#### 2. Using Azure Linux ARM64 VM

Deploy your application directly on an Azure Linux ARM64 VM, which you can create from an AArch64 ISO image (as demonstrated earlier).

## Run the performance tests using JMH inside the Azure Linux Container

JMH (Java Microbenchmark Harness) is a Java benchmarking framework developed by the JVM team at Oracle to measure the performance of small code snippets with high precision. It accounts for JVM optimizations like JIT and warm-up to ensure accurate and reproducible results. It measures the throughput, average latency, or execution time. Below steps help benchmark the Tomcat-like operation: 

Install Maven: 

```console 
$ tdnf install maven -y 
```
Create Benchmark Project: 

```bash { line_numbers = “true” }
mvn archetype:generate \ 
  -DinteractiveMode=false \ 
  -DarchetypeGroupId=org.openjdk.jmh \ 
  -DarchetypeArtifactId=jmh-java-benchmark-archetype \ 
  -DarchetypeVersion=1.37 \ 
  -DgroupId=com.example \ 
  -DartifactId=jmh-benchmark \ 
  -Dversion=1.0
cd jmh-benchmark
```
Edit the `src/main/java/com/example/MyBenchmark.java` file and add the below code on it: 

```
package com.example; 
import org.openjdk.jmh.annotations.Benchmark; 
public class MyBenchmark { 
 
    @Benchmarkpublic void benchmarkHttpResponse() { 
        String body = "Benchmarking a Tomcat-like operation"; 
        StringBuilder sb = new StringBuilder(); 
        sb.append("HTTP/1.1 200 OK\r\n"); 
        sb.append("Content-Type: text/plain\r\n"); 
        sb.append("Content-Length: ").append(body.length()).append("\r\n\r\n"); 
        sb.append(body); 
        if (sb.length() == 0) throw new RuntimeException(); // avoid DCE    } 
} 
```
This simulates HTTP response generation similar to Tomcat. 

Build the Benchmark:

```console
$ mvn clean install
```

After the build is complete, the JMH benchmark jar will be in the target/ directory.

Run the Benchmark: 

```console 
$ java -jar target/benchmarks.jar    
```

Output: 

```output 
Result "com.example.MyBenchmark.benchmarkHttpResponse": 
35600560.283 ?(99.9%) 121215.695 ops/s [Average] 
(min, avg, max) = (35293153.787, 35600560.283, 35831841.216), stdev = 161819.485 
CI (99.9%): [35479344.588, 35721775.978] (assumes normal distribution) 
# Run complete. Total time: 00:08:21 
```

### Benchmark summary on x86_64 based D4s_v6 general purpose Azure VM (JDK 11.0.27) 

The benchmark was executed inside an Azure Linux 3.0 Docker container on an x86_64 VM using the default Microsoft OpenJDK 11.0.27. 
| Metric                          | Value                                  |
|----------------------------------|----------------------------------------|
| **Java Version**               | OpenJDK 11.0.27                        |
| **Environment**                | Azure Linux 3.0 Docker container      |
| **Average Throughput**         | 21.88 million operations per second   |
| **Standard Deviation**         | ±76,941.944 ops/sec                   |
| **Confidence Interval (99.9%)**| [21.82M, 21.94M] ops/sec              |
| **Run Count**                  | 25 iterations                         |


### Benchmark summary on ARM64 based D4ps_v6 general purpose Azure VM (JDK 11.0.27): 

The benchmark was run using JMH inside an Azure Linux 3.0 Docker container on an Arm64 VM 
| Metric                          | Value                                  |
|----------------------------------|----------------------------------------|
| **Java Version**                                 | OpenJDK 11.0.27                        |
| **Environment**                                  | Azure Linux 3.0 Docker container      |
| **Average Throughput**                           | 35.60 million operations per second   |
| **Standard Deviation**                           | ±161,819.485 ops/sec                  |
| **Confidence Interval (99.9%)**                  | [35.48M, 35.72M] ops/sec              |
| **Run Count**                                | 25 iterations                         |


### Highlights from Azure Linux Arm64 Benchmarking (JDK 11.0.27)

- **Superior Throughput:** Achieved an average of 35.60 million ops/sec.
- **Stable Performance:** Low standard deviation (±161819.485 ops/sec) and a tight confidence interval suggest consistent benchmarking results.
- **Efficient Execution:** Demonstrates the efficiency of the Arm64 architecture for handling high-throughput Java workloads, even within a containerized Azure Linux environment.
- **Container Feasibility:** Confirms that Azure Linux 3.0 containers on Arm64 are a viable option for performance-sensitive Java applications, even in the absence of native Arm64 VM images.

## Run the performance tests using JMH on the custom azure linux arm vm

To run JMH-based Java [benchmarking on a custom Azure Linux VM](https://github.com/abhayPS11/my-data/edit/main/content/learning-paths/servers-and-cloud-computing/azure-arm64-vm/benchmarking.md#run-the-performance-tests-using-jmh-inside-the-azure-linux-container), follow these steps:

Now, Run the Benchmark on azure linux custom arm vm: 

```console 
$ java -jar target/benchmarks.jar    
```

Output: 

```output
Result "com.example.MyBenchmark.benchmarkHttpResponse":
35479679.586 ±(99.9%) 143641.773 ops/s [Average]
(min, avg, max) = (35074482.802, 35479679.586, 35723842.910), stdev = 191757.658
CI (99.9%): [35336037.813, 35623321.359] (assumes normal distribution)
# Run complete. Total time: 00:08:21
```

### JMH Benchmark Summary — x86_64 Azure Linux VM (D4s_v6)

| Metric                  | Value                            |
|-------------------------|----------------------------------|
| **Iterations (Count)**  | 25                               |
| **Average Throughput**  | 15.39 million operations/sec     |
| **Error (99.9%)**       | ±0.13 million ops/sec            |
| **Standard Deviation**  | ±0.17 million ops/sec            |
| **Min Throughput**      | 14.97 million ops/sec            |
| **Max Throughput**      | 15.58 million ops/sec            |


### JMH Benchmark Summary — Custom Azure Linux ARM64 VM

| Metric                  | Value                            |
|-------------------------|----------------------------------|
| **Iterations (Count)**  | 25                               |
| **Average Throughput**  | 35.48 million operations/sec     |
| **Error (99.9%)**       | ±0.14 million ops/sec            |
| **Standard Deviation**  | ±0.19 million ops/sec            |
| **Min Throughput**      | 35.07 million ops/sec            |
| **Max Throughput**      | 35.72 million ops/sec            |

### Highlights from Azure Linux Arm64 Benchmarking on cutom arm vm (JDK 11.0.27)

- **Average throughput:** 35.48 million operations/sec over 25 iterations
- **Throughput range**: 35.07M to 35.72M ops/sec
- **Performance stability**: Low standard deviation of ±0.19M ops/sec

