---
title: "Deploy Redis on Azure Cobalt 100"
weight: 1
layout: learningpathall
---

# Deploy Redis on Azure Cobalt 100 (ARM)

In this section, you will install and run Redis on an Azure Cobalt 100 Arm-based virtual machine and implement real-time messaging using Pub/Sub and Redis Streams.

## What You Will Learn

- Install Redis on ARM
- Run Redis server
- Implement Pub/Sub messaging
- Build event pipelines using Redis Streams

## Prepare the Environment

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential tcl git curl python3-pip
```

## Install Redis

```bash
cd /tmp
curl -O http://download.redis.io/redis-stable.tar.gz
tar xzf redis-stable.tar.gz
cd redis-stable
make -j$(nproc)
```

(Optional)

```bash
make test
```

## Start Redis Server
Terminal 1

```bash
cd /tmp/redis-stable
src/redis-server
```

## Verify Redis

Terminal 2

```bash
cd /tmp/redis-stable
src/redis-cli
PING
```

The output is similar to:
```output
127.0.0.1:6379> ping
PONG
```

## Real-Time Messaging (Pub/Sub)
Terminal 2 (Subscriber)

```bash
SUBSCRIBE chat_channel
```

Terminal 3 (Publisher)

```bash
cd /tmp/redis-stable
src/redis-cli
PUBLISH chat_channel "Hello from Cobalt ARM!"
```

## Redis Streams (Event Pipeline)
Terminal 3 (Producer)

```bash
XADD mystream * user jack action login
XADD mystream * user yan action purchase
```

Terminal 2 (Consumer)

```bash
XREAD COUNT 2 STREAMS mystream 0
```

Key Notes
Pub/Sub is real-time (no persistence)
Streams store data (durable messaging)
Streams enable replay and recovery
Summary

You have successfully:

Installed Redis on ARM
Built a real-time messaging system
Created an event pipeline using Redis Streams
