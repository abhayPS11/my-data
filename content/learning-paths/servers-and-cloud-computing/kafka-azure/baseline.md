---
title: Validate Kafka on Azure Cobalt 100 Arm64 VMs
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Baseline Functional Test (ZooKeeper + Kafka + Console Producer/Consumer) on Azure Cobalt 100 

After installing Kafka on your Arm64 virtual machine, you can perform simple baseline testing to validate that Kafka runs correctly and produces the expected output.

Kafka requires **ZooKeeper**. Since you downloaded 3.7.1 (still ships with ZooKeeper), we’ll use ZooKeeper here.

We need 5 terminals. Each one does a different job. ZooKeeper keeps things in order, Kafka runs the server, one makes a topic, one sends messages, and one reads them. This proves Kafka is working fine.

### Terminal 1 – Start ZooKeeper

This command goes into the Kafka folder and starts ZooKeeper, which is needed for Kafka to run properly. Keep this terminal open.

```console
cd /opt/kafka
bin/zookeeper-server-start.sh config/zookeeper.properties
```

### Terminal 2 – Start Kafka Broker

This command moves into the Kafka folder and starts the **Kafka broker** (the main server that sends and receives messages). Keep this running in its own terminal.
```console
cd /opt/kafka
bin/kafka-server-start.sh config/server.properties
```

### Terminal 3 – Create a Topic

This command creates a new Kafka topic named test-topic-kafka (like a channel where messages will be stored and shared) with 1 partition and 1 copy (replica).

```console
cd /opt/kafka
bin/kafka-topics.sh --create --topic test-topic-kafka --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```
You should see output similar to:

```output
Created topic test-topic-kafka.
```

- **Verify topic**

```console
bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```
You should see output similar to:

```output
__consumer_offsets
perf-test
test-topic
test-topic-kafka
test-topic-new
```

### Terminal 4 – Console Producer (Write Message)

This command starts the Kafka Producer, which lets you type and send messages into the `test-topic-kafka`. For example, you typed **hello from azure arm vm**, and this message will be delivered to any Kafka consumer subscribed to that topic.

```console
cd /opt/kafka
bin/kafka-console-producer.sh --topic test-topic --bootstrap-server localhost:9092
```
You should see output similar to:

```output
bin/kafka-console-producer.sh --topic test-topic --bootstrap-server localhost:9092
>hello from azure arm vm
>
```

### Terminal 5 – Console Consumer (Read Message)

This command starts the Kafka Consumer, which listens to the `test-topic-kafka` and displays all messages from the beginning. In your case, it showed **hello from azure arm vm**, proving that the producer’s message was successfully received.
```console
cd /opt/kafka
bin/kafka-console-consumer.sh --topic test-topic --from-beginning --bootstrap-server localhost:9092
```

You should see output similar to:

```output
hello from azure arm vm
```
Kafka baseline test is successful — ZooKeeper and the broker are running, the producer sends messages, and the consumer receives them. 

Now you can proceed to benchmarking Kafka’s performance on the Azure Cobalt 100 Arm VM.
