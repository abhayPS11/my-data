---
title: TPC-DS Benchmarking on Arm (Part 1 - Installation & Setup)
weight: 1

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install and Configure Big Data Stack on Arm VM

This guide walks you through installing and configuring Hadoop, Spark, and Hive on an Arm-based VM.

## Objective

In this part, you will:

* Install Java and required tools
* Install Hadoop, Spark, Hive
* Configure environment variables
* Setup Hadoop cluster (single node)
* Configure Hive metastore

## Environment

| Component | Value |
|----------|------|
| Platform | Arm64 VM |
| OS | Ubuntu 22.04/24.04 |
| RAM | ≥ 8 GB |
| Disk | ≥ 100 GB |


## Install dependencies

```console
sudo -i
apt update -y

apt install -y openjdk-17-jdk wget tar git curl unzip build-essential \
python3-pip mysql-server maven cmake ninja-build pkg-config libssl-dev
```


Verify:

```console
java -version
```

## Install Hadoop, Spark, Hive

```console
cd /opt

wget https://archive.apache.org/dist/hadoop/common/hadoop-3.3.1/hadoop-3.3.1-aarch64.tar.gz
tar -xvf hadoop-3.3.1-aarch64.tar.gz
ln -s hadoop-3.3.1 hadoop

wget https://archive.apache.org/dist/spark/spark-3.4.2/spark-3.4.2-bin-hadoop3.tgz
tar -xvf spark-3.4.2-bin-hadoop3.tgz
ln -s spark-3.4.2-bin-hadoop3 spark

wget https://archive.apache.org/dist/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz
tar -xvf apache-hive-3.1.3-bin.tar.gz
ln -s apache-hive-3.1.3-bin hive
```

## Environment variables

```console
cat >> ~/.bashrc <<'EOF'

export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-arm64
export HADOOP_HOME=/opt/hadoop
export SPARK_HOME=/opt/spark
export HIVE_HOME=/opt/hive

export PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$SPARK_HOME/bin:$SPARK_HOME/sbin:$HIVE_HOME/bin:$PATH

EOF

source ~/.bashrc
```

## Configure Hadoop

```cosnole
mkdir -p /opt/hadoop/dfs/name
mkdir -p /opt/dfs/data
mkdir -p /opt/hadoop/tmp
```

### core-site.xml

```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://spark-master:9000</value>
  </property>
</configuration>
hdfs-site.xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
</configuration>
```

## Start Hadoop

```console
hdfs namenode -format

$HADOOP_HOME/sbin/start-dfs.sh
$HADOOP_HOME/sbin/start-yarn.sh
```

Verify:

```console
jps
hdfs dfs -ls /
```

## Setup Hive Metastore

```console
mysql -u root
CREATE DATABASE hive_metastore;
CREATE USER 'hiveuser'@'localhost' IDENTIFIED BY '123456';
GRANT ALL PRIVILEGES ON hive_metastore.* TO 'hiveuser'@'localhost';
FLUSH PRIVILEGES;
```


## Initialize

```console
$HIVE_HOME/bin/schematool -dbType mysql -initSchema
```

## What you've learned

You installed:

Hadoop
Spark
Hive

You configured a working single-node cluster.
