---
title: Monitor MongoDB with mongostat
weight: 9

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Monitoring MongoDB Performance using mongostat
This guide demonstrates real-time MongoDB monitoring using **mongostat** on Arm64 Azure virtual machines. It **shows low-latency, stable insert, query, update, and delete operations**, with consistent memory usage and network throughput, providing a quick health-and-performance overview during benchmarking.

### Monitor with mongostat — Terminal 3

```console
mongostat 2
```
**mongostat** gives a one-line summary every 2 seconds of inserts, queries, updates, deletes, memory use and network I/O. It’s your quick health-and-throughput dashboard during the test.

You should see an output similar to:
```output
insert query update delete getmore command dirty used flushes vsize  res qrw arw net_in net_out conn                time
    *0    *0     *0     *0       0     4|0  0.0% 0.0%       0 3.54G 146M 0|0 0|0   664b   53.7k    6 Sep  1 11:08:41.011
    99     1     *0     *0       0     8|0  0.0% 0.0%       0 3.54G 148M 0|0 0|0  21.1k   61.4k   10 Sep  1 11:08:43.011
   355    13      4      4      72     8|0  0.0% 0.0%       0 3.54G 149M 0|0 0|0  86.1k    112k   10 Sep  1 11:08:45.011
     8    17      8      8     140     1|0  0.0% 0.0%       0 3.54G 149M 0|0 0|0  28.2k    137k   10 Sep  1 11:08:47.012
     4     9      4      4      80     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  15.5k   99.9k   10 Sep  1 11:08:49.011
     8    17      8      8     152     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  30.0k    146k   10 Sep  1 11:08:51.011
     4     8      4      4      76     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  15.2k    101k   11 Sep  1 11:08:53.012
     9    18      9      9     157     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  30.6k    152k   11 Sep  1 11:08:55.011
     4     8      4      4      80     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  15.7k    104k   11 Sep  1 11:08:57.011
     8    17      8      8     161     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  31.3k    157k   11 Sep  1 11:08:59.011
insert query update delete getmore command dirty used flushes vsize  res qrw arw net_in net_out conn                time
     4     8      4      4      80     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  15.8k    106k   11 Sep  1 11:09:01.012
     9    18      9      9     171     2|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  32.6k    161k   11 Sep  1 11:09:03.011
     4     8      4      4      85     0|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  16.3k    108k   11 Sep  1 11:09:05.011
     8    17      8      8     170     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  32.5k    164k   11 Sep  1 11:09:07.011
     4     8      4      4      89     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  17.0k    110k   11 Sep  1 11:09:09.012
     9    18      9      9     180     2|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  33.9k    168k   11 Sep  1 11:09:11.011
     4     8      4      4      89     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  17.0k    111k   11 Sep  1 11:09:13.011
     9    18      9      9     184     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  34.4k    171k   11 Sep  1 11:09:15.011
     4     8      4      4      94     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  17.6k    113k   11 Sep  1 11:09:17.011
     9    18      9      9     182     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  34.2k    171k   11 Sep  1 11:09:19.011
insert query update delete getmore command dirty used flushes vsize  res qrw arw net_in net_out conn                time
     4     8      4      4     100     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  18.6k    118k   11 Sep  1 11:09:21.012
     6    13      6      6     150     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  28.0k    149k   11 Sep  1 11:09:23.013
     6    13      6      6     146     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  26.7k    146k   11 Sep  1 11:09:25.013
     5    10      5      5     103     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  19.3k    120k   11 Sep  1 11:09:27.011
     8    17      8      8     198     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  36.1k    179k   11 Sep  1 11:09:29.011
     4     8      4      4     103     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  19.0k    119k   11 Sep  1 11:09:31.011
     8    17      8      8     206     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  37.7k    186k   11 Sep  1 11:09:33.012
     4     9      4      4     103     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  18.9k    120k   11 Sep  1 11:09:35.011
     7    15      7      7     179     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  32.9k    168k   11 Sep  1 11:09:37.012
     5    11      5      5     144     1|0  0.0% 0.0%       0 3.54G 150M 0|0 0|0  25.6k    144k   11 Sep  1 11:09:39.011
insert query update delete getmore command dirty used flushes vsize  res qrw arw net_in net_out conn                time
     5    10      5      5     125     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  23.0k    135k   11 Sep  1 11:09:41.011
     7    15      7      7     202     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  36.3k    182k   11 Sep  1 11:09:43.012
     4     9      4      4     112     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  20.2k    124k   11 Sep  1 11:09:45.011
     8    17      8      8     224     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  40.3k    197k   11 Sep  1 11:09:47.011
     4     8      4      4     112     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  20.2k    126k   11 Sep  1 11:09:49.013
     7    14      7      7     171     2|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  30.9k    162k   11 Sep  1 11:09:51.011
     2     4      2      2      63     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  11.0k   92.4k    6 Sep  1 11:09:53.011
```

### Explanation of mongostat Metrics

- **insert** - Number of document insert operations per second.
- **query** - Number of query operations (reads) per second.
- **update** - Number of document update operations per second.
- **delete** - Number of delete operations per second.
- **getmore** - Number of getMore operations per second (used when fetching more results from a cursor).
- **command** - Number of database commands executed per second (e.g., createIndex, count, aggregate).
  - command = number of regular commands | number of getLastError (GLE) commands
- **dirty/used** - Percentage of the WiredTiger cache that is dirty (not yet written to disk) and the percentage actively used.
- **flushes** - How many times data has been flushed to disk (per second).
- **vsize** - Virtual memory size of the mongod process.
- **res** - Resident memory size (actual RAM in use).
- **qrw arw** - Queued and active readers/writers:
  - `qrw` = queued read | queued write.
  - `arw` = active read | active write.
- **net_in/net_out** - Amount of network traffic coming into (net_in) and going out of (net_out) the database per second.
- **conn** - Number of active client connections.
- **time** - Timestamp of the sample.

{{% notice Note %}} Benchmarking was performed in both an Azure Linux 3.0 Docker container and an Azure Linux 3.0 virtual machine. The benchmark results were found to be relatively stable. {{% /notice %}}

Accordingly, this Learning path includes benchmark results from virtual machines only, for both x86 and Arm64 platforms. 

### Benchmark summary on x86_64:
The following benchmark results are collected on an x86_64 **D4s_v4 Azure virtual machine using the Azure Linux 3.0 image published by Ntegral Inc**.

| insert | query | update | delete | getmore | command | dirty | used | flushes | vsize  | res   | qrw | arw | net_in | net_out | conn   | time                   |
|--------|-------|--------|--------|---------|---------|-------|------|---------|--------|-------|-----|-----|--------|---------|--------|------------------------|
| 4      | 8     | 4      | 4      | 76      | 1/0     | 0.0%  | 0.0% | 0       | 2.54G  | 145M  | 0/0 | 0/0 | 15.1k | 99.2k  | 11 |Aug 13 10:16:38.605 |
| 8      | 17    | 8      | 8      | 152     | 1/0     | 0.0%  | 0.0% | 0       | 2.54G  | 145M  | 0/0 | 0/0 | 30.1k | 148k   | 11 |Aug 13 10:16:40.606 |
| 4      | 9     | 4      | 4      | 76      | 1/0     | 0.0%  | 0.0% | 0       | 2.54G  | 146M  | 0/0 | 0/0 | 15.0k | 102k   | 11 |Aug 13 10:16:42.604 |
| 8      | 17    | 8      | 8      | 161     | 1/0     | 0.0%  | 0.0% | 0       | 2.54G  | 146M  | 0/0 | 0/0 | 31.2k | 154k   | 11 |Aug 13 10:16:44.606 |
| 4      | 8     | 4      | 4      | 80      | 1/0     | 0.0%  | 0.0% | 0       | 2.54G  | 146M  | 0/0 | 0/0 | 15.7k | 105k   | 11 |Aug 13 10:16:46.607 |
| 8      | 17    | 8      | 8      | 150     | 2/0     | 0.0%  | 0.0% | 0       | 2.54G  | 146M  | 0/0 | 0/0 | 29.2k | 151k   | 11 |Aug 13 10:16:48.605 |
| 4      | 9     | 4      | 4      | 96      | 1/0     | 0.0%  | 0.0% | 0       | 2.54G  | 146M  | 0/0 | 0/0 | 18.6k | 114k   | 11 |Aug 13 10:16:50.606 |
| 7      | 15    | 7      | 7      | 138     | 1/0     | 0.0%  | 0.0% | 0       | 2.54G  | 147M  | 0/0 | 0/0 | 26.6k | 141k   | 11 |Aug 13 10:16:52.603 |
| 5      | 11    | 5      | 5      | 117     | 1/0     | 0.0%  | 0.0% | 0       | 2.54G  | 147M  | 0/0 | 0/0 | 22.3k | 128k   | 11 |Aug 13 10:16:54.605 |
| 6      | 12    | 6      | 6      | 141     | 1/0     | 0.0%  | 0.0% | 0       | 2.54G  | 148M  | 0/0 | 0/0 | 25.6k | 142k   | 11 |Aug 13 10:17:12.605 |


### Benchmark summary on Arm64
The following benchmark results are collected on an Arm64 **D4ps_v6 Azure virtual machine created from the Azure Linux 3.0 custom image using the AArch64 ISO**.

| insert | query | update | delete | getmore | command | dirty | used | flushes | vsize | res  | qrw  | arw  | net_in | net_out | conn | time                |
|--------|-------|--------|--------|---------|---------|-------|------|---------|-------|------|------|------|--------|---------|------|---------------------|
| 99     | 1     | *0     | *0     | 0       | 8|0     | 0.0%  | 0.0% | 0       | 3.54G | 148M | 0|0  | 0|0  | 21.1k  | 61.4k   | 10   | Sep  1 11:08:43.011 |
| 355    | 13    | 4      | 4      | 72      | 8|0     | 0.0%  | 0.0% | 0       | 3.54G | 149M | 0|0  | 0|0  | 86.1k  | 112k    | 10   | Sep  1 11:08:45.011 |
| 8      | 17    | 8      | 8      | 140     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 149M | 0|0  | 0|0  | 28.2k  | 137k    | 10   | Sep  1 11:08:47.012 |
| 4      | 9     | 4      | 4      | 80      | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 150M | 0|0  | 0|0  | 15.5k  | 99.9k   | 10   | Sep  1 11:08:49.011 |
| 8      | 17    | 8      | 8      | 152     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 150M | 0|0  | 0|0  | 30.0k  | 146k    | 10   | Sep  1 11:08:51.011 |
| 4      | 8     | 4      | 4      | 76      | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 150M | 0|0  | 0|0  | 15.2k  | 101k    | 11   | Sep  1 11:08:53.012 |
| 9      | 18    | 9      | 9      | 157     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 150M | 0|0  | 0|0  | 30.6k  | 152k    | 11   | Sep  1 11:08:55.011 |
| 4      | 8     | 4      | 4      | 80      | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 150M | 0|0  | 0|0  | 15.7k  | 104k    | 11   | Sep  1 11:08:57.011 |
| 8      | 17    | 8      | 8      | 161     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 150M | 0|0  | 0|0  | 31.3k  | 157k    | 11   | Sep  1 11:08:59.011 |




### Highlights from Azure Linux Arm64 Benchmarking

- **Insert, Query, Update, Delete Rates:** Operation throughput is stable, with inserts and queries typically in the 4–9 ops/sec range, while updates and deletes peak slightly higher during workload bursts.
- **Memory Usage:** Resident memory remains steady around 154 MB, and virtual memory around **3.54 GB**, confirming efficient memory handling on Arm64.
- **Network Activity:** Network traffic shows **net_in between ~15k–86k and net_out between 61k–157k**, consistent with the generated workload.
- **Connections:** Connections remain stable at **4**, demonstrating MongoDB can sustain concurrent client activity without spikes.
- **Command Execution:** Command, getmore, and dirty page activity stays minimal (**0–1**), indicating no internal blocking or resource contention.
- **Overall System Behavior:** MongoDB remains responsive under sustained load, with consistent operation rates and moderate resource usage across tests.
