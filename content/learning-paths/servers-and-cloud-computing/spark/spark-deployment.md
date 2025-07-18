---
title: Deploy GitHub Self-Hosted Runner on Google Axion C4A VM
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## Deploy Apache Spark on Google Axion C4A VM.

This Learning Path shows how to deploy Apache Spark on a Google Cloud C4A Arm VM running Ubuntu.It covers installing Java, Scala, Maven, and building Spark from source, followed by functional validation through baseline testing. 
Finally, it includes benchmarking to compare Spark’s performance on Arm64 versus x86 architectures—optimizing data processing workloads on cost-efficient Arm-based infrastructure.

### Install Required Packages 

```console
$ sudo dnf update -y
$ sudo dnf install -y java-17-openjdk java-17-openjdk-devel git maven wget nano curl unzip
```
Verify Java installation: 
```console
$ java -version
```
Install scala:
```console
$ curl -O https://downloads.lightbend.com/scala/2.13.16/scala-2.13.16.tgz
$ tar -xzf scala-2.13.16.tgz
$ sudo mv scala-2.13.16 /opt/scala
$ export PATH=/opt/scala/bin:$PATH
$ source ~/.bashrc
```
Verify Scala Installation:

```console
$ scala -version
```

### Clone Apache Spark Repository 
```console
$ git clone https://github.com/apache/spark.git 
$ cd spark
```
This will clone the latest Spark version 

### Build Spark (for Arm)

To build Spark
```console
$ ./build/mvn -DskipTests clean package 
```
This process may take 20–30 minutes depending on the VM. 

### Set Environment Variables 
Add to `~/.bashrc` or `~/.zshrc` for persistenc

```cosole
$ export SPARK_HOME=~/spark 
$ export PATH=$SPARK_HOME/bin:$PATH
```
Apply changes immediatel

```console
$ source ~/.bashrc 
```

### Verify Spark Installation 

```console
$ spark-shell --version 
```
You should see output like: 

```output
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 4.1.0-SNAPSHOT
      /_/

Using Scala version 2.13.16, OpenJDK 64-Bit Server VM, 17.0.15
Branch master
Compiled by user gcpuser on 2025-07-17T06:04:20Z
```
Using Scala version 2.13.16, Java HotSpot(TM) 64-Bit Server VM, OpenJDK 17.

Since Apache Spark is installed successfully on your GCP C4A Arm VM, let's now perform simple baseline testing to validate that Spark runs correctly and gives expected output. 

## Spark Baseline Test

Create a simple Spark job file: 
```console
$ nano ~/spark_baseline_test.scala`
```
Below is this content of **spark_baseline_test.scala** file:

```scala
val data = Seq(1, 2, 3, 4, 5) 
val distData = spark.sparkContext.parallelize(data) 
 
// Basic transformation and action 
val squared = distData.map(x => x * x).collect() 
 
println("Squared values: " + squared.mkString(", ")) 
```
Code Explanation:
  - val data = Seq(1, 2, 3, 4, 5) >>>>>️ Creates a local Scala sequence of integers. 
  - val distData = spark.sparkContext.parallelize(data)>>>️ Converts the sequence into a distributed RDD for parallel processing. 
  - val squared = distData.map(x => x * x).collect() ️>>>> Squares each number in the RDD and collects the result back to the driver. 
  - println("Squared values: " + squared.mkString(", "))>>>>>>Prints the squared values as a comma-separated string.

### Run the Test in Spark Shell

Run it in the interactive shell: 
```console
$ spark-shell < ~/spark_baseline_test.scala 
```
You should see: 
```output
Squared values: 1, 4, 9, 16, 25
```
This confirms that Spark is working correctly with its driver, executor, and cluster manager in local mode. 


