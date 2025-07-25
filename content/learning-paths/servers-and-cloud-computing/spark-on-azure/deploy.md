---
title: Install Apache Spark and Perform Baseline Testing
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---


### Platform Overview
Whether you're using an Azure Linux 3.0 Docker container or a virtual machine created from a custom Azure Linux 3.0 image, the deployment and benchmarking steps remain the same.

### Working inside Azure Linux 3.0 Docker container
The Azure Linux Container Host is an operating system image that's optimized for running container workloads on Azure Kubernetes Service (AKS). Microsoft maintains the Azure Linux Container Host and based it on CBL-Mariner, an open-source Linux distribution created by Microsoft.
To know more about Azure Linux 3.0, kindly refer [What is Azure Linux Container Host for AKS](https://learn.microsoft.com/en-us/azure/azure-linux/intro-azure-linux). Azure Linux 3.0 offers support for Aarch64. However, the standalone virtual machine image for Azure Linux 3.0 or CBL Mariner 3.0 is not available for Arm.

Hence, to use the default software stack provided by the Microsoft team, this guide will focus on creating a docker container with Azure Linux 3.0 as a base image and will build and run the Spark application inside the container.

### Create Azure Linux 3.0 Docker Container 
The [Microsoft Artifact Registry](https://mcr.microsoft.com/en-us/artifact/mar/azurelinux/base/core/about) offers updated docker image for the Azure Linux 3.0.  

To create a docker container, install docker, and then follow the below instructions: 

```console
$ sudo docker run -it --rm mcr.microsoft.com/azurelinux/base/core:3.0
``` 

The default container startup command is bash. tdnf and dnf are the default package managers.

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
$ spark-submit --version
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
Since Apache Spark is installed successfully on your Arm virtual machine, let's now perform simple baseline testing to validate that Spark runs correctly and gives expected output.

## Baseline Testing
Create a file named **test_spark.py** and add the following simple PySpark script to test your setup:

```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("Test").getOrCreate()
df = spark.createDataFrame([(1, "ARM64"), (2, "Azure")], ["id", "name"])
df.show()
spark.stop()
```
Code Explanation:
This code is a basic Apache Spark example in PySpark, demonstrating how to create a Spark session, build a DataFrame, display its content, and gracefully stop the Spark context.

What it does, step by step:
- **Create Spark Session** – Initializes a Spark application named “Test” to run PySpark jobs.
- **Create DataFrame** – Builds a small in-memory table with two rows and two columns (id, name).
- **Show Data** – Displays the contents of the DataFrame in tabular format.
- **Stop Spark** – Gracefully shuts down the Spark session after the job completes.

Run the script using:
```console
$ spark-submit test_spark.py
```
This command submits the PySpark job to Spark for execution.

Output:

```output
25/07/22 05:16:00 INFO CodeGenerator: Code generated in 10.545923 ms
25/07/22 05:16:00 INFO SparkContext: SparkContext is stopping with exitCode 0.
+---+-----+
| id| name|
+---+-----+
|  1|ARM64|
|  2|Azure|
+---+-----+
```
Output summary:

- Spark successfully ran the script, generating code in **~10.5 ms.**
- Displayed the DataFrame with test data: **[1, "ARM64"]** and **[2, "Azure"]**.
- Spark exited cleanly **(exitCode 0)**, confirming the setup works on Arm64.

