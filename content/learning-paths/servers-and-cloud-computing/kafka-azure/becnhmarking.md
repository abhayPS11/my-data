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
1000000 records sent, 347463.516331 records/sec (33.14 MB/sec), 582.94 ms avg latency, 928.00 ms max latency, 605 ms 50th, 898 ms 95th, 921 ms 99th, 926 ms 99.9th.
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
2025-09-02 06:00:37:795, 2025-09-02 06:00:38:647, 95.3674, 111.9336, 1000000, 1173708.9202, 293, 559, 170.6036, 1788908.7657
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
| Start Time              | End Time                | Data Consumed (MB) | MB/sec  | Messages Consumed | Msg/sec     | Rebalance Time (ms) | Fetch Time (ms) | Fetch MB/sec | Fetch Msg/sec  |
|--------------------------|-------------------------|--------------------|---------|-------------------|-------------|----------------------|-----------------|--------------|----------------|
| 2025-09-02 06:00:37:795 | 2025-09-02 06:00:38:647 | 95.3674            | 111.93  | 1,000,000         | 1,173,708.92 | 293                  | 559             | 170.60       | 1,788,908.77   |

### Producer Performance Test
| Records Sent | Records/sec   | MB/sec | Avg Latency (ms) | Max Latency (ms) | 50th (ms) | 95th (ms) | 99th (ms) | 99.9th (ms) |
|--------------|---------------|--------|------------------|------------------|-----------|-----------|-----------|-------------|
| 1,000,000    | 347,463.5163  | 33.14  | 582.94           | 928.00           | 605       | 898       | 921       | 926         |

## Benchmark summary on x86_64:
Here is a summary of the benchmark results collected on x86_64 **D4s_v6 Ubuntu virtual machine**.           

### Consumer Performance Test 

| Start Time              | End Time                | Data Consumed (MB) | MB/sec  | Messages Consumed | Msg/sec      | Rebalance Time (ms) | Fetch Time (ms) | Fetch MB/sec | Fetch Msg/sec  |
|--------------------------|-------------------------|--------------------|---------|-------------------|--------------|----------------------|-----------------|--------------|----------------|
| 2025-09-02 06:38:05:878 | 2025-09-02 06:38:06:657 | 95.3674            | 122.42  | 1,000,000         | 1,283,697.05 | 206                  | 573             | 166.44       | 1,745,200.70   |

### Producer Performance Test

| Records Sent | Records/sec   | MB/sec | Avg Latency (ms) | Max Latency (ms) | 50th (ms) | 95th (ms) | 99th (ms) | 99.9th (ms) |
|--------------|---------------|--------|------------------|------------------|-----------|-----------|-----------|-------------|
| 1,000,000    | 239,923.22    | 22.88  | 914.41           | 1356.00          | 1004      | 1295      | 1342      | 1353        |

## Benchmark comparison insights

When comparing the results on Arm64 vs x86_64 virtual machines:

- **Consumer test** achieved **~112 MB/sec** throughput, processing **1M messages in under 1 sec** with peak fetch rate ~1.79M msg/sec.
- **Producer test** sustained **~347K records/sec (33 MB/sec)** with sub-second average latency (~583 ms).
- Overall, Arm64 Azure virtual machine shows **high throughput and low latency** for both Kafka producer and consumer workloads.

You have now benchmarked Kafka on an Azure Cobalt 100 Arm64 virtual machine and compared results with x86_64.
