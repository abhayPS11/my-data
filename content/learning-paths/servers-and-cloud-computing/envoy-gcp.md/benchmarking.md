---
title: Envoy Benchmarking
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Envoy Benchmarking with Siege

**Siege** is a lightweight HTTP load testing and benchmarking tool that simulates concurrent users making requests to a target service. It is useful for **Envoy benchmarking** because it measures availability, throughput, response time, and failure rates under load—helping evaluate Envoy’s performance as a proxy under real-world traffic conditions.

### Install Siege(Build from Source)

1. Install required build tools

```console
sudo dnf groupinstall -y "Development Tools"
sudo dnf install -y wget make gcc
```
2. Download, extract and build Siege source

```console
wget http://download.joedog.org/siege/siege-4.1.6.tar.gz
tar -xvzf siege-4.1.6.tar.gz
cd siege-4.1.6
./configure
make
sudo make install
```
These commands prepare Siege for your system, build (compile) it, and then install it so you can run it from anywhere.

3. Verify installation

```console
siege --version
```
This checks if Siege is installed properly and shows the version number.
```output
SIEGE 4.1.6

Copyright (C) 2023 by Jeffrey Fulmer, et al.
This is free software; see the source for copying conditions.
There is NO warranty; not even for MERCHANTABILITY or FITNESS
FOR A PARTICULAR PURPOSE.
```
### Envoy Benchmarking

1. Ensure Envoy is Running

To make sure your Envoy proxy is up with your config file (listening on port 10000 for example):

```console
envoy -c envoy_config.yaml --base-id 1
```
This runs the Envoy proxy with your configuration file (envoy_config.yaml) so it can start listening for requests.

2. Verify with curl from the another terminal:

```
curl -v http://localhost:10000/get
```
Running from another terminal returns a **200 OK** status, confirming that Envoy is running and successfully proxying requests.

3. Run a Time-based Load Test

Benchmark for a fixed time instead of request count:

```console
siege -c 20 -t30S http://localhost:10000/get
```
This runs a load test where 20 users hit Envoy continuously for 30 seconds. After this, Siege will show performance results.

The output should look similar to:

```output
** SIEGE 4.1.6
** Preparing 20 concurrent users for battle.
The server is now under siege...
HTTP/1.1 200     0.06 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.06 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.15 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.18 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.30 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.18 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.04 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.35 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.58 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.63 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.47 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.76 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.39 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.26 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.53 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.31 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.32 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.40 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.34 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.45 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.06 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.73 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.06 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.17 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.63 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.21 secs:     382 bytes ==> GET  /get
HTTP/1.1 502     0.83 secs:     122 bytes ==> GET  /get
HTTP/1.1 200     1.56 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.96 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 502     1.70 secs:     122 bytes ==> GET  /get
HTTP/1.1 200     0.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.60 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.76 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.65 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.32 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.80 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.45 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.07 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.64 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.84 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.23 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.13 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.44 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.36 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.19 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.76 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.40 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.52 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.05 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.17 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.13 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.64 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.50 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.04 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.10 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.42 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.95 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.26 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.79 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.65 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.92 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.54 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.76 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.29 secs:     382 bytes ==> GET  /get
HTTP/1.1 502     0.16 secs:     122 bytes ==> GET  /get
HTTP/1.1 200     0.13 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.60 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.18 secs:     382 bytes ==> GET  /get
HTTP/1.1 502     0.47 secs:     122 bytes ==> GET  /get
HTTP/1.1 200     0.30 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.23 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.18 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.60 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.50 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.14 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.33 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.28 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.05 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.19 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.25 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.36 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.40 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.47 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.28 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.20 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.73 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.24 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.72 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.78 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.27 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.46 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.68 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.37 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.26 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.09 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.65 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.20 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.57 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.51 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.39 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.58 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.64 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.15 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.12 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.83 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.14 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.06 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.46 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.99 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.26 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.01 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.45 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.04 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.36 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.33 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.46 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.71 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.09 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.65 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.97 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.73 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.73 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.31 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.78 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.39 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.46 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.05 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.31 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.74 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.37 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.06 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.27 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.31 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.77 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     5.19 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.05 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.38 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.42 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.75 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.09 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.79 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.76 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.16 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.35 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.17 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.77 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.72 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.25 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.66 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.78 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.18 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.24 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.13 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.26 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.00 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.58 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.14 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.40 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.59 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.80 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.71 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.55 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.54 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.32 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.89 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.99 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.66 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.64 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.82 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.96 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.17 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.91 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.59 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.33 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.19 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.12 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.27 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.92 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.49 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.90 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.85 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.09 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.18 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.31 secs:     382 bytes ==> GET  /get
HTTP/1.1 502     0.15 secs:     122 bytes ==> GET  /get
HTTP/1.1 200     0.91 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.14 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.30 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.66 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.20 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.24 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.37 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.86 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.14 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.68 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.90 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.38 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.14 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.23 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.12 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.20 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.05 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.28 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.78 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.85 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.24 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.17 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.07 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.30 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.68 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.44 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.42 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.24 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.74 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.51 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.95 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.10 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.91 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.92 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.23 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.32 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.52 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.54 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.01 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.45 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     4.21 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.80 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.62 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.19 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.33 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.25 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.30 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.15 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.13 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.18 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.25 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.19 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.14 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.26 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.43 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.72 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.87 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.16 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.43 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.34 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.06 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.04 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.83 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.41 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.23 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.58 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.05 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.14 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.27 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.60 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.58 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.02 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.93 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.70 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.27 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.01 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.01 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.49 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.00 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.85 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.69 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.29 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.15 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.28 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.65 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     4.01 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.13 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.91 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.35 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.36 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     5.30 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.04 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.91 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.05 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.17 secs:     382 bytes ==> GET  /get
HTTP/1.1 502     1.22 secs:     122 bytes ==> GET  /get
HTTP/1.1 200     1.51 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.65 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.79 secs:     382 bytes ==> GET  /get
HTTP/1.1 502     0.56 secs:     122 bytes ==> GET  /get
HTTP/1.1 200     0.21 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.77 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.84 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.72 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.64 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.19 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.91 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.06 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.84 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.06 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.32 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.29 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.31 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.92 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.10 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.73 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.97 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.48 secs:     382 bytes ==> GET  /get
HTTP/1.1 502     2.04 secs:     122 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.68 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.40 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.64 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.14 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.94 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.20 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.18 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     7.39 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.89 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.69 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.67 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.90 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.15 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.68 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.16 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.09 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.07 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.07 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.30 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.20 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.66 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.47 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     6.53 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.99 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.31 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.21 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.17 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.10 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.28 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.42 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.54 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.80 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.48 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.36 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.61 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.85 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.87 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.86 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.52 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.92 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.77 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.06 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.13 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.58 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.92 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.84 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.07 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.04 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.17 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.63 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.49 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.01 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.02 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.15 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.39 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.20 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.34 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.06 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.01 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.42 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.10 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.15 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.09 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.33 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.05 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.43 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.73 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.15 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.30 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.43 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.98 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.15 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.02 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.39 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.80 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.90 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.89 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.38 secs:     382 bytes ==> GET  /get
HTTP/1.1 502     0.84 secs:     122 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.55 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.16 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.33 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.09 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.90 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.71 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.89 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.56 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.82 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.36 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.31 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.76 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.07 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.41 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.16 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.32 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.69 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.07 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.14 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.17 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     5.33 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.13 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.25 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.31 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.60 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.10 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.95 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.32 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.82 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.72 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.38 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.05 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.38 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.95 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.84 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.04 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.58 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.61 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.93 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.63 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.64 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.02 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.90 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.93 secs:     382 bytes ==> GET  /get
HTTP/1.1 502     0.18 secs:     122 bytes ==> GET  /get
HTTP/1.1 200     0.82 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.52 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.60 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.38 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.69 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.37 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.21 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.31 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.04 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     4.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.81 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.33 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.20 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.62 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.24 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.15 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.96 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.29 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.62 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.01 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.65 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.80 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.34 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.14 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.77 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.93 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.94 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.12 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.01 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.10 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.19 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.87 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.82 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.39 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.24 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.28 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.44 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.41 secs:     382 bytes ==> GET  /get
HTTP/1.1 502     0.49 secs:     122 bytes ==> GET  /get
HTTP/1.1 200     0.32 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.11 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.91 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.25 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.70 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.10 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.13 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.43 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.83 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.08 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.22 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.04 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.82 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.21 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.44 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.33 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.79 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.16 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.50 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     3.44 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.37 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.19 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.85 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.32 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.23 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.78 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.02 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.88 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     1.43 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     0.03 secs:     382 bytes ==> GET  /get
HTTP/1.1 200     2.17 secs:     382 bytes ==> GET  /get

Lifting the server siege...
Transactions:                    598 hits
Availability:                  98.19 %
Elapsed time:                  30.11 secs
Data transferred:               0.22 MB
Response time:                  0.97 secs
Transaction rate:              19.86 trans/sec
Throughput:                     0.01 MB/sec
Concurrency:                   19.36
Successful transactions:         598
Failed transactions:              11
Longest transaction:            7.39
Shortest transaction:           0.02
```

### Siege Operations & Latency Metrics

- **Transactions**: Total number of completed requests during the benchmark.
- **Availability**: Percentage of requests that returned a successful response.
- **Elapsed Time**: Total time taken to run the benchmark test.
- **Data Transferred**: Total amount of data exchanged during the test.
- **Response Time**: Average time taken for the server to respond to each request.
- **Transaction Rate**: Number of requests processed per second.
- **Throughput**: Volume of data transferred per second.
- **Concurrency**: Average number of simultaneous connections maintained.
- **Successful Transactions**: Total number of requests completed successfully.
- **Failed Transactions**: Total number of requests that failed.
- **Longest Transaction**: Maximum response time observed for a single request.
- **Shortest Transaction**: Minimum response time observed for a single request.

### Benchmark summary on x86_64:
The following  benchmark results are collected on a c3-standard-4 (4 vCPU, 2 core, 16 GB Memory) x86_64 environment, running RHEL 9.

### Siege Benchmark Results

| Metric                 | Value        | Metric                   | Value          |
|-------------------------|--------------|---------------------------|----------------|
| Transactions            | 786 hits     | Availability              | 98.87 %        |
| Elapsed time            | 30.44 secs   | Data transferred          | 0.28 MB        |
| Response time           | 0.75 secs    | Transaction rate          | 25.82 trans/sec|
| Throughput              | 0.01 MB/sec  | Concurrency               | 19.32          |
| Successful transactions | 786          | Failed transactions       | 9              |
| Longest transaction     | 5.67 secs    | Shortest transaction      | 0.03 secs      |


### Benchmark summary on Arm64:
The following  benchmark results are collected on a c4a-standard-4 (4 vCPU, 16 GB Memory) Arm64 environment, running RHEL 9.

### Siege Benchmark Results

| Metric                 | Value        | Metric                   | Value          |
|-------------------------|--------------|---------------------------|----------------|
| Transactions            | 598 hits     | Availability              | 98.19 %        |
| Elapsed time            | 30.11 secs   | Data transferred          | 0.22 MB        |
| Response time           | 0.97 secs    | Transaction rate          | 19.86 trans/sec|
| Throughput              | 0.01 MB/sec  | Concurrency               | 19.36          |
| Successful transactions | 598          | Failed transactions       | 11             |
| Longest transaction     | 7.39 secs    | Shortest transaction      | 0.02 secs      |

### Highlights from GCP C4A Arm virtual machine
- Achieved **598 successful transactions** with **98.19% availability**, though **11 failed** transactions were recorded.
- Average response time was **0.97** secs, with a transaction rate of **19.86 trans/sec** and throughput of 0.01 MB/sec.
- Transactions ranged from a **shortest of 0.02 secs** to a l**ongest of 7.39 secs**, showing some latency variability.
