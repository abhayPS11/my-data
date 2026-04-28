---
title: Integrate Alluxio with Apache Spark and Optimize Performance
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Integrate Alluxio with Apache Spark

This section demonstrates how to integrate Alluxio with Apache Spark, enable caching, and optimize data access performance.

## Install Apache Spark

```bash
cd ~
wget https://archive.apache.org/dist/spark/spark-3.4.2/spark-3.4.2-bin-hadoop3.tgz
tar -xvzf spark-3.4.2-bin-hadoop3.tgz

sudo mv spark-3.4.2-bin-hadoop3 /opt/spark
sudo chown -R $USER:$USER /opt/spark
```

## Configure Spark environment

```bash
echo 'export SPARK_HOME=/opt/spark' >> ~/.bashrc
echo 'export PATH=$PATH:$SPARK_HOME/bin' >> ~/.bashrc
source ~/.bashrc
```

## Connect Spark with Alluxio

```bash
nano $SPARK_HOME/conf/spark-defaults.conf
```

**Add:**

```bash
spark.hadoop.fs.alluxio.impl=alluxio.hadoop.FileSystem
spark.driver.extraClassPath=/opt/alluxio/client/alluxio-2.9.4-client.jar
spark.executor.extraClassPath=/opt/alluxio/client/alluxio-2.9.4-client.jar
```

## Create dataset

```bash
rm -rf /mnt/data/demo
mkdir -p /mnt/data/demo
```

```bash
for i in {1..100000}; do
  echo "record $i - alluxio spark learning" >> /mnt/data/demo/data.txt
done
```

**Verify:**

```bash
wc -l /mnt/data/demo/data.txt
```

## Run Spark

```bash
spark-shell
```

## Load data via Alluxio

```bash
val df = spark.read.text("alluxio:///demo/data.txt")
df.count()
```

**Expected output:**

```output
100000
```

## Enable caching

```bash
df.cache()
df.count()
```

## Measure performance

**First run:**

```bash
val t1 = System.nanoTime()
df.count()
val t2 = System.nanoTime()
println((t2 - t1)/1e9 + " seconds")
```

**Second run (cached):**

```bash
val t3 = System.nanoTime()
df.count()
val t4 = System.nanoTime()
println((t4 - t3)/1e9 + " seconds")
```

**Expected behavior:**

```text
First run reads from disk (slower)
Second run reads from memory cache (faster)
```

**Verify in Alluxio UI:**

**Open:**

```text
http://<VM-IP>:19999
```

**Observe:**

- Increased memory usage
- Cached file blocks
- Improved access speed

## Compare with direct file access

```bash
val df1 = spark.read.text("file:///mnt/data/demo/data.txt")
val df2 = spark.read.text("alluxio:///demo/data.txt")
```

This demonstrates the performance benefit of Alluxio.

## Key concepts

- Alluxio acts as a caching layer between compute and storage
- Frequently accessed data is stored in memory
- Spark reads cached data instead of disk

## What you've learned and what's next

You have successfully:

- Integrated Spark with Alluxio
- Enabled distributed caching
- Measured performance improvements
- Validated results using real data

You are now ready to extend this setup with cloud storage, Spark SQL, and distributed clusters.
