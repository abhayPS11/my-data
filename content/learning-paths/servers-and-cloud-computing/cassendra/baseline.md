---
title: Apache Cassandra baseline testing on Google Axion C4A Arm Virtual machine
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---


Since Cassandra has been successfully installed on your GCP C4A Arm virtual machine, please follow these steps to make sure that it is running.

## Baseline Testing for Apache Cassandra 5.0.5

This guide helps verify the installation and perform baseline testing of **Apache Cassandra 5.0.5**.

## Start Cassandra

Run Cassandra in the background:

```console
cassandra -R
```

- `-R` → allows Cassandra to run in the background.  
- First startup might take **30–60 seconds**.  

Check logs:

```console
tail -f ~/cassandra/logs/system.log
```
Look for "Startup complete" at the end.

### Check Cassandra Status
```console
nodetool status
```
You should see an output similar to:

```output
Datacenter: datacenter1
=======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address    Load        Tokens  Owns (effective)  Host ID                               Rack
UN  127.0.0.1  162.51 KiB  16      100.0%            78774686-39f3-47e7-87c3-3abc4f02a835  rack1
```

- `UN = Up & Normal` → means Cassandra is running fine.

### Connect with CQLSH (Cassandra Query Shell)

```console
cqlsh
```
You’ll enter the CQL (Cassandra Query Language) shell.

### Create a Keyspace (like a database)

```sql
CREATE KEYSPACE testks WITH replication = {'class':'SimpleStrategy','replication_factor' : 1};
```
Check if created:

```sql
DESCRIBE KEYSPACES;
```

You should see an output similar to:

```output
cqlsh> DESCRIBE KEYSPACES;

system       system_distributed  system_traces  system_virtual_schema
system_auth  system_schema       system_views   testks
```

### Create a Table

```sql
USE testks;

CREATE TABLE users (
   id UUID PRIMARY KEY,
   name text,
   age int
);
```

### Insert Data

```sql
INSERT INTO users (id, name, age) VALUES (uuid(), 'Alice', 30);
INSERT INTO users (id, name, age) VALUES (uuid(), 'Bob', 25);
```

### Query Data

```sql
SELECT * FROM users;
```

You should see an output similar to:

```output
 id                                   | age | name
--------------------------------------+-----+-------
 c08dafde-17f0-4a4a-82b8-54455bb07836 |  25 |   Bob
 d47eb93c-3988-4aa1-bc85-9561500a6893 |  30 | Alice

(2 rows)
```

This baseline test verifies that Cassandra 5.0.5 is installed and running correctly on the VM. It confirms the node status, allows connection via `cqlsh`, and ensures basic operations like creating a keyspace, table, inserting, and querying data work as expected.
