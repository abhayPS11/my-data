---
title: Deploy Apache Spark on Google Axion C4A virtual machine
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## Deploy Apache Spark on Google Axion C4A virtual machine

This Learning Path shows how to deploy Apache Spark on a Google Cloud C4A Arm virtual machine running Red Hat Enterprise Linux. It covers installing Java, Scala, Maven, and Spark, followed by functional validation through baseline testing. 
Finally, it includes benchmarking to compare Spark’s performance on Arm64 versus x86 architectures—optimizing data processing workloads on cost-efficient Arm-based infrastructure.

### Install Required Packages 

```console
$ sudo tdnf update -y
$ sudo tdnf install -y java-17-openjdk java-17-openjdk-devel git maven wget nano curl unzip tar
```
Verify Java installation: 
```console
$ java -version
```

### Install Apache Spark on Arm
```console
$ wget https://downloads.apache.org/spark/spark-3.5.6/spark-3.5.6-bin-hadoop3.tgz
$ tar -xzf spark-3.5.6-bin-hadoop3.tgz
$ sudo mv spark-3.5.6-bin-hadoop3 /opt/spark
```
### Set Environment Variables 
Add this line to ~/.bashrc or ~/.zshrc to make the change persistent across terminal sessions.

```cosole
$ echo 'export SPARK_HOME=/opt/spark' >> ~/.bashrc
$ echo 'export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin' >> ~/.bashrc

```
Apply changes immediately

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
   /___/ .__/\_,_/_/ /_/\_\   version 3.5.6
      /_/

Using Scala version 2.12.18, OpenJDK 64-Bit Server VM, 17.0.15
```

Since Apache Spark is installed successfully on your GCP C4A Arm virtual machine, let's now perform simple baseline testing to validate that Spark runs correctly and gives expected output. 

## Spark Baseline Test

Create a simple Spark job file: 
```console
$ nano ~/spark_baseline_test.scala
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
This code is a basic Apache Spark example in Scala, demonstrating how to create an RDD (Resilient Distributed Dataset), perform a transformation, and collect results.

What it does, step by step:

- **val data = Seq(1, 2, 3, 4, 5)** : Creates a local Scala sequence of integers.
- **val distData = spark.sparkContext.parallelize(data)** : Uses parallelize to convert the local sequence into a distributed RDD (so Spark can operate on it in parallel across cluster nodes or CPU cores).
- **val squared = distData.map(x => x * x).collect()** : `map(x => x * x)` squares each element in the list, `.collect()` brings all the transformed data back to the driver program as a regular Scala collection.
- **println("Squared values: " + squared.mkString(", "))** : Prints the squared values, joined by commas.


### Run the Test in Spark Shell

Run the test in the interactive shell: 
```console
$ spark-shell < ~/spark_baseline_test.scala 
```
You should see: 
```output
Squared values: 1, 4, 9, 16, 25
```
This confirms that Spark is working correctly with its driver, executor, and cluster manager in local mode. 
