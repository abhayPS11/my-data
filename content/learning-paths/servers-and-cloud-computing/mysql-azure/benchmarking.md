---
title: Benchmark MySQL
weight: 7

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Benchmark MySQL on Azure Cobalt 100 Arm-based instances and x86_64 instances


## Steps MySQL Benchmarking with mysqlslap

Step 1: Ensure MySQL is Running

```console
sudo /usr/local/mysql/bin/mysqld_safe --datadir=/usr/local/mysql/data &
ps -ef | grep mysqld
```

2. Create a Test Database

```console
mysql -u root -p   # or mysql -u root if insecure
```

3. CREATE DATABASE benchmark_db;

```sql
CREATE DATABASE benchmark_db;
USE benchmark_db;
```
4. Create a Table and Populate Data

```sql
CREATE TABLE benchmark_table (
    record_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50),
    score INT
);
```
Insert some sample rows manually

```sql
INSERT INTO benchmark_table (username,score) VALUES 
('John',100),('Jane',200),('Mike',300);
```

Or populate automatically with 1000 rows:

```sql
DELIMITER //
CREATE PROCEDURE populate_benchmark_data()
BEGIN
    DECLARE i INT DEFAULT 1;
    WHILE i <= 1000 DO
        INSERT INTO benchmark_table (username, score)
        VALUES (CONCAT('Player', i), i*10);
        SET i = i + 1;
    END WHILE;
END //
DELIMITER ;

CALL populate_benchmark_data();
DROP PROCEDURE populate_benchmark_data;
```

5. Run a Basic Read Benchmark

```console
mysqlslap   --user=root   --password="NewStrongPassword!"   --host=127.0.0.1   --concurrency=10   --iterations=5   --query="INSERT INTO benchmark_db.benchmark_table (username,score) VALUES('TestUser',123);"   --create-schema=benchmark_db
```

```output
mysqlslap: [Warning] Using a password on the command line interface can be insecure.
Benchmark
        Average number of seconds to run all queries: 0.020 seconds
        Minimum number of seconds to run all queries: 0.020 seconds
        Maximum number of seconds to run all queries: 0.022 seconds
        Number of clients running queries: 10
        Average number of queries per client: 1
```

```console
mysqlslap --user=root --password="NewStrongPassword!"  --host=127.0.0.1 --concurrency=10 --iterations=5 --query="SELECT * FROM benchmark_db.benchmark_table WHERE record_id < 500;"  --create-schema=benchmark_db  --verbose | tee -a /tmp/mysqlslap_benchmark.log
```
```output
mysqlslap: [Warning] Using a password on the command line interface can be insecure.
Benchmark
        Average number of seconds to run all queries: 0.009 seconds
        Minimum number of seconds to run all queries: 0.009 seconds
        Maximum number of seconds to run all queries: 0.010 seconds
        Number of clients running queries: 10
        Average number of queries per client: 1
```

## Benchmark Results Table Explained:


{{% notice Note %}}
Benchmark results on Azure Linux 3.0 were consistent across both Docker containers and virtual machines.
{{% /notice %}}


## Benchmark summary on Arm64:
For easier comparison, shown here is a summary of benchmark results collected on an Arm64 `D4ps_v6` Azure virtual machine created from a custom Azure Linux 3.0 image using the AArch64 ISO.

| Benchmark Type | Avg Time (s) | Min Time (s) | Max Time (s) | Clients | Queries per Client |
|----------------|-------------|--------------|--------------|---------|------------------|
| Read (SELECT)  | 0.009       | 0.009        | 0.010        | 10      | 1                |
| Write (INSERT) | 0.020       | 0.020        | 0.022        | 10      | 1                |


## Benchmark summary on x86_64:
Shown here is a summary of the benchmark results collected on an `x86_64` `D4s_v4` Azure virtual machine using the Azure Linux 3.0 image published by Ntegral Inc.                                        | On         | 31254          | 31346          | 78         | 0.7         | 1490.3         | 1.2X     |

| Benchmark Type | Avg Time (s) | Min Time (s) | Max Time (s) | Clients | Queries per Client |
|----------------|-------------|--------------|--------------|---------|------------------|
| Read (SELECT)  | 0.017       | 0.016        | 0.018        | 10      | 1                |
| Write (INSERT) | 0.038       | 0.030        | 0.044        | 10      | 1                |

## Benchmark comparison insights

When comparing the results on Arm64 vs x86_64 virtual machines:


You have now benchmarked MySql on an Azure Cobalt 100 Arm64 virtual machine and compared results with x86_64.
