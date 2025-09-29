---
title: Install Apache Cassandra
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Apache Cassandra Installation on SUSE VM

This guide provides step-by-step instructions to install **Apache Cassandra 5.0.5** on a SUSE Linux VM.

### Update System Packages

```console
sudo zypper refresh
sudo zypper update -y
```

### Install Java

```console
sudo zypper install -y java-17-openjdk java-17-openjdk-devel
```

### Download Cassandra

```console
wget https://downloads.apache.org/cassandra/5.0.5/apache-cassandra-5.0.5-bin.tar.gz
```
{{% notice Note %}}
Apache Cassandra 5.0 is a major release introducing significant performance, usability, and scalability enhancements. Key features include Storage Attached Indexes (SAI) for flexible querying, Trie-based memtables/SSTables for better efficiency, and the Unified Compaction Strategy (UCS) for automated data management. It also supports JDK 17 for up to 20% performance gains and adds vector search for AI applications. The release marks the end-of-life for the 3.x series, urging users to upgrade for continued support.
You can view [this release note](https://cassandra.apache.org/_/blog/Apache-Cassandra-5.0-Announcement.html)

The [Arm Ecosystem Dashboard](https://developer.arm.com/ecosystem-dashboard/) recommends PHP version 5.0.0 as the minimum recommended on the Arm platforms.
{{% /notice %}}

### Extract and Setup Cassandra

```console
tar -xvzf apache-cassandra-5.0.5-bin.tar.gz
mv apache-cassandra-5.0.5 ~/cassandra
```

### Verify Installation

```console
cassandra -v
```
You should see an output similar to:
```output
5.0.5
```
Cassendra's installation is complete. You can now proceed with the baseline testing.
