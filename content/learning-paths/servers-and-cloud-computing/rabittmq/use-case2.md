---
title: RabbitMQ WhatsApp Notification Use Case
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## WhatsApp Notification Use Case using RabbitMQ  
This document demonstrates a **real-world asynchronous messaging use case** where RabbitMQ is used to process WhatsApp notifications reliably using a worker-based architecture.

### Use Case Overview

In many production systems, sending WhatsApp notifications must be:
- Reliable
- Asynchronous
- Independent of the main application flow

RabbitMQ is used as a **message broker** to decouple message production from message consumption.

### Architecture Flow

1. Application publishes a message to RabbitMQ
2. RabbitMQ routes the message to a queue
3. A Python worker consumes the message
4. The worker simulates sending a WhatsApp notification

### Prerequisites

- GCP SUSE Arm64 virtual machine
- RabbitMQ is installed and running
- RabbitMQ Management Plugin enabled
- Python 3.8+
- `pika` Python client library installed

```console
sudo zypper install -y python3 python3-pip
pip3 install pika
```

### RabbitMQ Topology

**Exchanges**
- **notifications (direct):** Routes WhatsApp notification messages based on an exact routing key match.

**Queue**
- **whatsapp.notifications (durable):** Stores WhatsApp messages persistently until they are consumed by a worker.

**Binding**
- Exchange: **notifications** – Connects the exchange to the WhatsApp notification queue.
- Routing key: **whatsapp** – Ensures only WhatsApp-related messages are routed.
- Queue: **whatsapp.notifications**– Final destination where messages are delivered for processing.

### Declare RabbitMQ Resources

```console
./rabbitmqadmin declare exchange \
  name=notifications \
  type=direct \
  durable=true

./rabbitmqadmin declare queue \
  name=whatsapp.notifications \
  durable=true

./rabbitmqadmin declare binding \
  source=notifications \
  destination=whatsapp.notifications \
  routing_key=whatsapp
```

**Validate the setup:**

```console
./rabbitmqadmin list queues name messages
./rabbitmqadmin list exchanges name type
./rabbitmqadmin list bindings
```

```output
gcpuser@lpprojectsusearm64:~> ./rabbitmqadmin list queues name messages
+------------------------+----------+
|          name          | messages |
+------------------------+----------+
| jobs                   | 0        |
| order.events           | 1        |
| testqueue              | 1        |
| whatsapp.notifications | 0        |
+------------------------+----------+
gcpuser@lpprojectsusearm64:~> ./rabbitmqadmin list exchanges name type
+--------------------+---------+
|        name        |  type   |
+--------------------+---------+
|                    | direct  |
| amq.direct         | direct  |
| amq.fanout         | fanout  |
| amq.headers        | headers |
| amq.match          | headers |
| amq.rabbitmq.trace | topic   |
| amq.topic          | topic   |
| events             | topic   |
| notifications      | direct  |
+--------------------+---------+
gcpuser@lpprojectsusearm64:~> ./rabbitmqadmin list bindings
+---------------+------------------------+------------------------+
|    source     |      destination       |      routing_key       |
+---------------+------------------------+------------------------+
|               | jobs                   | jobs                   |
|               | order.events           | order.events           |
|               | testqueue              | testqueue              |
|               | whatsapp.notifications | whatsapp.notifications |
| events        | order.events           | order.*                |
| notifications | whatsapp.notifications | whatsapp               |
+---------------+------------------------+------------------------+
gcpuser@lpprojectsusearm64:~>
```

### WhatsApp Worker Implementation
The worker attaches as a **blocking consumer** to the `whatsapp.notifications` queue and processes incoming messages.

`whatsapp_worker.py`

```python
import pika
import json
import time

RABBITMQ_HOST = "localhost"
RABBITMQ_VHOST = "/"
RABBITMQ_USER = "guest"
RABBITMQ_PASS = "guest"
QUEUE_NAME = "whatsapp.notifications"

credentials = pika.PlainCredentials(RABBITMQ_USER, RABBITMQ_PASS)

parameters = pika.ConnectionParameters(
    host=RABBITMQ_HOST,
    virtual_host=RABBITMQ_VHOST,
    credentials=credentials,
    heartbeat=60
)

print("[DEBUG] Connecting to RabbitMQ...")
connection = pika.BlockingConnection(parameters)
channel = connection.channel()

print("[DEBUG] Declaring queue...")
channel.queue_declare(queue=QUEUE_NAME, durable=True)

print("[DEBUG] Setting QoS...")
channel.basic_qos(prefetch_count=1)

print("✅ WhatsApp Worker started. Waiting for messages...")

def send_whatsapp(ch, method, properties, body):
    data = json.loads(body.decode())
    print(f"[Worker] Sending WhatsApp message to {data['phone']}")
    print(f"[Worker] Message content: {data['message']}")

    # Simulate external WhatsApp API call
    time.sleep(1)

    print("[Worker] Message sent successfully ✅")
    ch.basic_ack(delivery_tag=method.delivery_tag)

channel.basic_consume(
    queue=QUEUE_NAME,
    on_message_callback=send_whatsapp,
    auto_ack=False
)

print("[DEBUG] Starting consumer loop (this should block)...")
channel.start_consuming()
```

### Start the Worker
Run the worker in a dedicated terminal session:

```console
python3 whatsapp_worker.py
```
output:

```output
[DEBUG] Connecting to RabbitMQ...
[DEBUG] Declaring queue...
[DEBUG] Setting QoS...
✅ WhatsApp Worker started. Waiting for messages...
[DEBUG] Starting consumer loop (this should BLOCK)...
[Worker] Sending WhatsApp message to +911234567890
[Worker] Message content: Your order #1234 has been confirmed
[Worker] Message sent successfully ✅
[Worker] Sending WhatsApp message to +911234567890
```

The process must block without returning to the shell prompt.

### Publish a Test Message
From another terminal:

```console
./rabbitmqadmin publish \
  exchange=notifications \
  routing_key=whatsapp \
  payload='{"phone":"+911234567890","message":"Hello from RabbitMQ"}'
```

### Message Consumption Validation
The worker terminal displays:

```output
[DEBUG] Connecting to RabbitMQ...
[DEBUG] Declaring queue...
[DEBUG] Setting QoS...
✅ WhatsApp Worker started. Waiting for messages...
[DEBUG] Starting consumer loop (this should BLOCK)...
[Worker] Sending WhatsApp message to +911234567890
[Worker] Message content: Your order #1234 has been confirmed
[Worker] Message sent successfully ✅
[Worker] Sending WhatsApp message to +911234567890
[Worker] Message content: Your order #1234 has been confirmed
[Worker] Message sent successfully ✅
[Worker] Sending WhatsApp message to +9111
[Worker] Message content: Test-1
[Worker] Message sent successfully ✅
[Worker] Sending WhatsApp message to +911234567890
[Worker] Message content: Validation test
[Worker] Message sent successfully ✅
[Worker] Sending WhatsApp message to +911234567890
[Worker] Message content: Hello from RabbitMQ
[Worker] Message sent successfully ✅
[Worker] Sending WhatsApp message to +911234567890
[Worker] Message content: Hello from RabbitMQ
[Worker] Message sent successfully ✅
[Worker] Sending WhatsApp message to +911234567890
[Worker] Message content: FINAL validation test
[Worker] Message sent successfully ✅
[Worker] Sending WhatsApp message to +911234567890
[Worker] Message content: FINAL validation test
[Worker] Message sent successfully ✅
```


### Verify Queue State

```console
./rabbitmqadmin list queues name messages consumers
```

Expected output:

```output
+------------------------+----------+-----------+
|          name          | messages | consumers |
+------------------------+----------+-----------+
| jobs                   | 1        | 0         |
| order.events           | 1        | 0         |
| testqueue              | 1        | 0         |
| whatsapp.notifications | 0        | 1         |
+------------------------+----------+-----------+
```


```

