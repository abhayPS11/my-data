---
title: Install Apache Spark and Perform Baseline Testing
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---


### Platform Overview
Whether you're using an Azure Linux 3.0 Docker container or a VM created from a custom Azure Linux 3.0 image, the deployment and benchmarking steps remain the same.

### Working inside Azure Linux 3.0 Docker container
The Azure Linux Container Host is an operating system image that's optimized for running container workloads on Azure Kubernetes Service (AKS). Microsoft maintains the Azure Linux Container Host and based it on CBL-Mariner, an open-source Linux 
distribution created by Microsoft. 
To know more about Azure Linux 3.0, kindly refer [What is Azure Linux Container Host for AKS](https://learn.microsoft.com/en-us/azure/azure-linux/intro-azure-linux). Azure Linux 3.0 offers support for Aarch64. However, the standalone VM image for Azure Linux 3.0 or CBL Mariner 3.0 is not available for Arm.

Hence, to use the default software stack provided by the Microsoft team, this guide will focus on creating a docker container with Azure Linux 3.0 as a base image and will build 
and run the Spark application inside the container.

### Create Azure Linux 3.0 Docker Container 
The [Microsoft Artifact Registry](https://mcr.microsoft.com/en-us/artifact/mar/azurelinux/base/core/about) offers updated docker image for the Azure Linux 3.0.  

To create a docker container, install docker, and then follow the below instructions: 

```console
$ sudo docker run -it --rm mcr.microsoft.com/azurelinux/base/core:3.0
``` 

The default container startup command is bash. tdnf and dnf are the default package managers.

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
## Baseline Testing
Since Apache Spark is installed successfully on your Arm VM, let's now perform simple baseline testing to validate that Spark runs correctly and gives expected output.

Launch Spark Shell:
```console
spark-shell
```
Expected: It should start the REPL and provide access to `spark` and `sc` contexts.

Run a Simple Job:
```scala
val data = Seq(1, 2, 3, 4, 5)
val distData = sc.parallelize(data)
val result = distData.reduce(_ + _)
println(s"Result: $result")
```
- **val data = Seq(1, 2, 3, 4, 5)**: Creates a local in-memory Scala sequence of integers.
- **val distData = sc.parallelize(data)**: Converts the sequence into a distributed RDD using SparkContext.
- **val result = distData.reduce(_ + _)**: Aggregates the elements in the RDD by summing them in parallel.
- **println(s"Result: $result")**: Prints the final sum of all elements to the console.

Output:
```output
Result: 15
val data: Seq[Int] = List(1, 2, 3, 4, 5)
val distData: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[0] at parallelize at <console>:2
val result: Int = 15
```
Output summery:
- The list List(1, 2, 3, 4, 5) is converted into a distributed RDD using sc.parallelize().
- A reduce action is performed across the RDD to compute the sum, demonstrating Spark's parallel processing.
- The final result of the distributed computation is 15.
