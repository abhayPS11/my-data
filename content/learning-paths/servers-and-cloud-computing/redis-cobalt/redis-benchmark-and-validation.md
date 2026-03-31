---
title: Benchmark and validate Redis on Azure Cobalt 100
weight: 2

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Benchmark and validate Redis performance

This section demonstrates how to implement production-grade event processing using Redis Streams consumer groups, simulate real workloads using Python, and benchmark Redis performance on an Azure Cobalt 100 Arm-based virtual machine.

You will validate Redis for high-throughput, low-latency workloads on Arm infrastructure.


## Create a consumer group

Consumer groups enable scalable and reliable message processing by distributing work across multiple consumers.

```bash
XGROUP CREATE mystream mygroup 0 MKSTREAM
```

The output is similar to:

```output
OK
```

## Consume messages using a consumer group

Read messages from the stream as part of the consumer group:

```bash
XREADGROUP GROUP mygroup consumer1 COUNT 1 STREAMS mystream >
```

The output is similar to:

```output
1) 1) "mystream"
   2) 1) 1) "1774856537955-0"
         2) 1) "event"
            2) "msg-0"
```

## Acknowledge processed messages

Acknowledge messages after processing to prevent re-delivery:

```bash
XACK mystream mygroup <message-id>
```

Replace `<message-id>` with the ID returned from the previous command.

## Install Python Redis client

Install the Redis Python library to simulate real-world producer and consumer applications:

```bash
pip3 install redis
```

## Create a Python producer

Create a producer script to send events to the Redis stream:

```python
import redis

r = redis.Redis(host='localhost', port=6379)

for i in range(10):
    r.xadd("mystream", {"event": f"msg-{i}"})
    print(f"Produced msg-{i}")
```

Run the producer:

```bash
python3 producer.py
```

The output is similar to:

```output
Produced msg-0
Produced msg-1
...
Produced msg-9
```

## Create a Python consumer

Create a consumer script to read and process messages:

```python
import redis

r = redis.Redis(host='localhost', port=6379, decode_responses=True)

while True:
    messages = r.xreadgroup("mygroup", "consumer1", {"mystream": ">"}, count=1, block=5000)
    
    for stream, msgs in messages:
        for msg_id, data in msgs:
            print(f"Consumed {msg_id}: {data}")
            r.xack("mystream", "mygroup", msg_id)
```


**Run the consumer:**

```bash
python3 consumer.py
```

The output is similar to:

```output
Consumed 1774856537955-0: {'event': 'msg-0'}
Consumed 1774856537955-1: {'event': 'msg-1'}
```

## Benchmark Redis performance

Run the Redis benchmark tool to measure throughput and latency:

```bash
cd /tmp/redis-stable
src/redis-benchmark -q -n 100000 -c 50
```

The output is similar to:

```output
SET: 130000 requests per second
GET: 128000 requests per second
XADD: 131000 requests per second
```

These results demonstrate high throughput and efficient performance on the Arm architecture.

## Benchmark Pub/Sub performance

Run a publish benchmark to evaluate messaging throughput:

```bash
src/redis-benchmark -t publish -n 100000
```

{{% notice Note %}}
Ensure a subscriber is active before running this benchmark to observe message delivery.
{{% /notice %}}

## Monitor Redis metrics

Use the Redis INFO command to inspect runtime statistics:

```bash
src/redis-cli info stats
```

The output is similar to:

```output
total_commands_processed:2300144
keyspace_hits:800158
rejected_connections:0
Key observations
Redis achieves ~130K operations per second on Arm
Latency remains under 1 millisecond
Streams provide reliable, persistent messaging
Consumer groups enable scalable processing
No connection rejections observed during benchmarking
Real-world use cases
Real-time messaging systems
Event-driven microservices
Data ingestion pipelines
Streaming analytics workloads
```

## What you've learned 

You have successfully:

- Implemented consumer groups for scalable processing
- Built Python-based producer and consumer applications
- Benchmarked Redis performance on Cobalt 100
- Validated Redis for high-throughput workloads

