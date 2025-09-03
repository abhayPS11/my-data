---
title: Benchmarking with Official Kafka Tools
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Benchmark Kafka on Azure Cobalt 100 Arm-based instances and x86_64 instances

Kafka’s official performance tools (**kafka-producer-perf-test.sh** and **kafka-consumer-perf-test.sh**) let you generate test workloads, measure message throughput, and record end-to-end latency.

## Steps for Kafka Benchmarking 

Before starting the benchmark, ensure that **ZooKeeper** and the **Kafka broker** are already running in separate terminals. 

Now, open two new terminals—one for the **producer benchmark** and another for the **consumer benchmark**.

### Terminal - A Producer Benchmark

The producer benchmark measures how fast Kafka can send messages, reporting throughput and latency percentiles.

```console
cd /opt/kafka
bin/kafka-producer-perf-test.sh \
  --topic test-topic-kafka \
  --num-records 1000000 \
  --record-size 100 \
  --throughput -1 \
  --producer-props bootstrap.servers=localhost:9092
```
You should see output similar to:

```output
1000000 records sent, 252589.0 records/sec (24.09 MB/sec), 850.85 ms avg latency, 1219.00 ms max latency, 851 ms 50th, 1184 ms 95th, 1210 ms 99th, 1218 ms 99.9th.
```
### Terminal - B Consumer benchmark

The consumer benchmark measures how fast Kafka can read messages from the topic, reporting throughput and total messages consumed.

```console
cd /opt/kafka
bin/kafka-consumer-perf-test.sh \
  --topic test-topic-kafka \
  --bootstrap-server localhost:9092 \
  --messages 1000000 \
  --timeout 30000 \
  --threads 1
```
You should see output similar to:

```output
start.time, end.time, data.consumed.in.MB, MB.sec, data.consumed.in.nMsg, nMsg.sec, rebalance.time.ms, fetch.time.ms, fetch.MB.sec, fetch.nMsg.sec
2025-09-03 06:07:13:616, 2025-09-03 06:07:17:545, 95.3674, 24.2727, 1000001, 254517.9435, 3354, 575, 165.8564, 1739132.1739
```

## Benchmark summary on Arm64:

- **Messages Processed** – Total number of messages handled during the test.  
- **Records/sec** – Rate of messages sent or consumed per second.  
- **MB/sec** – Data throughput in megabytes per second.  
- **Avg Latency (ms)** – Average delay in sending messages (producer only).  
- **Max Latency (ms)** – Longest observed delay in sending messages (producer only).  
- **50th (ms)** – Median latency (half the messages were faster, half slower).  
- **95th (ms)** – Latency below which 95% of messages were delivered.  
- **99th (ms)** – Latency below which 99% of messages were delivered.  
- **99.9th (ms)** – Latency below which 99.9% of messages were delivered.  

## Benchmark summary on Arm64:

Here is a summary of benchmark results collected on an Arm64 **D4ps_v6 Ubuntu virtual machine**.

### Consumer Performance Test

| Metric                     | Value       | Unit          |
|-----------------------------|-------------|---------------|
| Total Time Taken           | 3.929       | Seconds       |
| Data Consumed              | 95.3674     | MB            |
| Throughput (Data)          | 24.2727     | MB/sec        |
| Messages Consumed          | 1,000,001   | Messages      |
| Throughput (Messages)      | 254,517.94  | Messages/sec  |
| Rebalance Time             | 3354        | Milliseconds  |
| Fetch Time                 | 575         | Milliseconds  |
| Fetch Throughput (Data)    | 165.8564    | MB/sec        |
| Fetch Throughput (Messages)| 1,739,132.17| Messages/sec  |

### Producer Performance Test

| Metric | Records Sent | Records/sec | Throughput | Average Latency | Maximum Latency | 50th Percentile Latency | 95th Percentile Latency | 99th Percentile Latency | 99.9th Percentile Latency |
|--------|--------------|-------------|------------|-----------------|-----------------|-------------------------|-------------------------|-------------------------|---------------------------|
| Value  | 1,000,000    | 252,589.0   | 24.09      | 850.85          | 1219.00         | 851                     | 1184                    | 1210                    | 1218                      |
| Unit   | Records      | Records/sec | MB/sec     | ms              | ms              | ms                      | ms                      | ms                      | ms                        |

## Benchmark summary on x86_64:
Here is a summary of the benchmark results collected on x86_64 **D4s_v6 Ubuntu virtual machine**.           

### Consumer Performance Test 

| Metric                     | Value       | Unit          |
|-----------------------------|-------------|---------------|
| Total Time Taken            | 3.828       | Seconds       |
| Data Consumed               | 95.3674     | MB            |
| Throughput (Data)           | 24.9131     | MB/sec        |
| Messages Consumed           | 1,000,001   | Messages      |
| Throughput (Messages)       | 261,233.28  | Messages/sec  |
| Rebalance Time              | 3244        | Milliseconds  |
| Fetch Time                  | 584         | Milliseconds  |
| Fetch Throughput (Data)     | 163.3004    | MB/sec        |
| Fetch Throughput (Messages) | 1,712,330.48| Messages/sec  |

### Producer Performance Test

| Metric | Records Sent | Records/sec | Throughput | Average Latency | Maximum Latency | 50th Percentile Latency | 95th Percentile Latency | 99th Percentile Latency | 99.9th Percentile Latency |
|--------|--------------|-------------|------------|-----------------|-----------------|-------------------------|-------------------------|-------------------------|---------------------------|
| Value  | 1,000,000    | 244,200.2   | 23.29      | 857.17          | 1162.00         | 907                     | 1131                    | 1152                    | 1160                      |
| Unit   | Records      | Records/sec | MB/sec     | ms              | ms              | ms                      | ms                      | ms                      | ms                        |

## Benchmark comparison insights

When comparing the results on Arm64 vs x86_64 virtual machines:

- The Kafka **consumer** achieved ~24.27 MB/sec throughput, processing ~254K messages per second.  
- The Kafka **producer** sustained ~24.09 MB/sec throughput, with an average latency of ~851 ms and a peak latency of ~1219 ms.  
- These results confirm stable Kafka performance on the Azure Ubuntu Arm64 Virtula machine, suitable for baseline validation and benchmarking. 

You have now benchmarked Kafka on an Azure Cobalt 100 Arm64 virtual machine and compared results with x86_64.
