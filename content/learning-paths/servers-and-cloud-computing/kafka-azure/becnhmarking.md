---
title: Benchmarking with Official Kafka Tools
weight: 7

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
1000000 records sent, 257003.341043 records/sec (24.51 MB/sec), 880.43 ms avg latency, 1422.00 ms max latency, 799 ms 50th, 1353 ms 95th, 1412 ms 99th, 1421 ms 99.9th.
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
WARNING: option [threads] and [num-fetch-threads] have been deprecated and will be ignored by the test
start.time, end.time, data.consumed.in.MB, MB.sec, data.consumed.in.nMsg, nMsg.sec, rebalance.time.ms, fetch.time.ms, fetch.MB.sec, fetch.nMsg.sec
2025-08-28 12:05:56:787, 2025-08-28 12:05:57:558, 95.3674, 123.6932, 1000000, 1297016.8612, 261, 510, 186.9950, 1960784.3137
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

For easier comparison, shown here is a summary of benchmark results collected on two different Arm64 environments: a **Docker container running Azure Linux 3.0 hosted on a D4ps_v6 Ubuntu-based Azure virtual machine**, and a **D4ps_v6 Azure virtual machine created from the Azure Linux 3.0 custom image using the AArch64 ISO**.

| Test Type | Records Processed | Throughput (records/sec) | Throughput (MB/sec) | Avg Latency (ms) | Max Latency (ms) | 50th %ile (ms) | 95th %ile (ms) | 99th %ile (ms) | 99.9th %ile (ms) |
|-----------|-------------------|--------------------------|---------------------|------------------|------------------|----------------|----------------|----------------|------------------|
| Producer  | 1,000,000         | 257,003                  | 24.51               | 880              | 1422             | 799            | 1353           | 1412           | 1421             |
| Consumer  | 1,000,000         | 1,297,017                | 123.69              | N/A              | N/A              | N/A            | N/A            | N/A            | N/A              |

