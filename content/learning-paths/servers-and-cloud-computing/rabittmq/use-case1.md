---
title: RabbitMQ Use Case – Event Processing with Python Workers
weight: 5
layout: learningpathall
---

## RabbitMQ Use Case – Event Processing with Python Workers

This document demonstrates a **practical RabbitMQ use case** using:

- Topic exchange–based routing
- Durable queues and bindings
- A Python-based worker using the `pika` client
- Message publishing and consumption validation

The use case models an **event-driven system**, where order-related events are published and processed asynchronously by workers.


### Use Case Overview

**Scenario:**  
An application publishes order-related events (`order.created`, `order.updated`, etc.) to RabbitMQ. A background worker consumes these events from a queue and processes them independently.

**Key Components:**

- **Exchange:** `events` (topic)
- **Queue:** `order.events`
- **Routing Pattern:** `order.*`
- **Worker Language:** Python
- **Client Library:** `pika`


### Prerequisites

- RabbitMQ installed and running
- RabbitMQ management plugin enabled
- Python 3 installed
- Network access to RabbitMQ broker

### Declare a Topic Exchange
Create a durable topic exchange to route events based on routing keys.

```console
./rabbitmqadmin declare exchange name=events type=topic durable=true
```

### Declare a Durable Queue
Create a durable queue to store order-related events.

```console
./rabbitmqadmin declare queue name=order.events durable=true
```

You should see an output similar to:
```output
queue declared
```

### Bind Queue to Exchange
Bind the queue to the exchange using a topic routing pattern.

```console
./rabbitmqadmin declare binding source=events destination=order.events routing_key="order.*"
```

You should see an output similar to:
```output
binding declared
```

This binding ensures the queue receives all messages with routing keys such as:
- order.created
- order.updated
- order.completed

### Publish an Event Message
Publish a sample order event to the exchange.

```console
./rabbitmqadmin publish exchange=events routing_key="order.created" payload='{"order_id":123}
```

You should see an output similar to:
```output
Message published
```

### Install Python Dependencies
Install pip and the pika RabbitMQ client library.

```console
sudo zypper install -y python3-pip
pip install pika
```

### Create the Worker Script
Create a Python worker file to process messages from a queue.

```console
vi worker.py
```

**worker.py:**

```python
import pika
import time
import json

# RabbitMQ broker address
RABBITMQ_IP = "localhost"

connection = pika.BlockingConnection(
    pika.ConnectionParameters(host=RABBITMQ_IP)
)
channel = connection.channel()

# Ensure queue exists
channel.queue_declare(queue='jobs', durable=True)

print("Worker started. Waiting for jobs...")

def process_job(ch, method, properties, body):
    job = json.loads(body.decode())
    print(f"[Worker] Received job: {job}")

    # Simulate processing
    time.sleep(2)

    # Acknowledge message
    ch.basic_ack(delivery_tag=method.delivery_tag)

# Fair dispatch configuration
channel.basic_qos(prefetch_count=1)

channel.basic_consume(
    queue='jobs',
    on_message_callback=process_job
)

channel.start_consuming()
```

### Start the Worker
Run the worker process.

```console
python3 worker.py
```

You should see an output similar to:
```output
Worker started. Waiting for jobs...
```

### Publish Job Messages
From another terminal, publish a job message.

```console
./rabbitmqadmin publish routing_key=jobs payload='{"job":"test1"}'
```

**Worker output:**

```output
Worker started. Waiting for jobs...
[Worker] Received job: {'job': 'test1'}
```

Publish another job:

```console
./rabbitmqadmin publish routing_key=jobs payload='{"job":"hello1"}'
```

**Worker output:**

```output
Worker started. Waiting for jobs...
[Worker] Received job: {'job': 'hello1'}
```

## Use Case Validation

- Event routing via topic exchanges functions correctly  
- Durable queues and acknowledgments ensure reliable message processing  
- Worker-based consumption supports safe and controlled job execution


This use case demonstrates how RabbitMQ enables reliable, decoupled, and scalable event processing using topic-based routing and Python workers.
The setup provides a strong foundation for production-grade, message-driven architectures on GCP SUSE Arm64 virtual machines.
