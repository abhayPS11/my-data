---
title: Java Benchmarking Using JMH
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Test the Java application for Performance 
Now that you’ve built and run the Tomcat-like response, you can use it to test the JVM performance using JMH. You can also use it to test the performance difference between Cobalt 100 instances and other similar D series x86_64 based instances. 


## Run the performance tests using JMH 

JMH (Java Microbenchmark Harness) is a Java benchmarking framework developed by the JVM team at Oracle to measure the performance of small code snippets with high precision. It accounts for JVM optimizations like JIT and warm-up to ensure accurate and reproducible results. It measures the throughput, average latency, or execution time. Below steps help benchmark the Tomcat-like operation: 

As noted before, the steps to benchmark remain the same, whether it's a Docker container or a custom VM.

Install Maven: 

```console 
tdnf install maven -y 
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
mvn clean install
```

After the build is complete, the JMH benchmark jar will be in the target/ directory.

Run the Benchmark: 

```console 
java -jar target/benchmarks.jar    
```


### Benchmark summary on x86_64:

The following benchmark results are collected on two different x86_64 environments: a Docker container running Azure Linux 3.0 hosted on a D4s_v6 Ubuntu-based Azure VM, and a D4s_v4 Azure VM created from the Azure Linux 3.0 image published by Ntegral Inc.

| Metric     | Value on Docker Container |  Value on Virtual Machine
|----------------------------------|----------------------------------------|-------------------|
| **Java Version**               | OpenJDK 11.0.27                        |    OpenJDK 11.0.27  |
| **Run Count**                  | 25 iterations                         |     25 iterations   |
| **Average Throughput**         | 21.88M ops/sec   |   15.39M ops/sec |
| **Standard Deviation**         | ±0.0769M ops/sec                  |   ±0.17M ops/sec  |
| **Confidence Interval (99.9%)**| [21.82M, 21.94M] ops/sec              |   [14.97M, 15.58M] ops/sec


### Benchmark summary on Arm64: 

The following benchmark results are collected on two different Arm64 environments: a Docker container running Azure Linux 3.0 hosted on a D4ps_v6 Ubuntu-based Azure VM, and a D4ps_v6 Azure VM created from the Azure Linux 3.0 image created by the Aarch64 ISO.

| Metric     | Value on Docker Container |  Value on Virtual Machine
|----------------------------------|----------------------------------------|-------------------|
| **Java Version**               | OpenJDK 11.0.27                        |    OpenJDK 11.0.27  |
| **Run Count**                  | 25 iterations                         |     25 iterations   |
| **Average Throughput**         | 35.60M ops/sec                    |   35.48M ops/sec |
| **Standard Deviation**         | ±161,819.485 ops/sec                   |   ±191757.658 ops/sec  |
| **Confidence Interval (99.9%)**| [35.48M, 35.72M] ops/sec               |   [35.347M, 35.62M] ops/sec


### Highlights from Azure Linux Arm64 Benchmarking (JDK 11.0.27)

- **Superior Throughput:** Achieved an average of 35.60M ops/sec on Docker and 35.48M ops/sec on the virtual machine..
- **Stable Performance:** Standard deviation on Docker is ±161,819.485 ops/sec and on the virtual machine is ±191,757.658 ops/sec, indicating consistent benchmarking results.
- **Efficient Execution:** Demonstrates the efficiency of the Arm64 architecture for handling high-throughput Java workloads, even within a containerized Azure Linux environment.
- **Container Feasibility:** Confirms that Azure Linux 3.0 containers on Arm64 are a viable option for performance-sensitive Java applications, even in the absence of native Arm64 VM images.
