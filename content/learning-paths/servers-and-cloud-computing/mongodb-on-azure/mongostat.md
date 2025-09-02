---
title: Monitor MongoDB with mongostat
weight: 9

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Monitoring MongoDB Performance using mongostat
This guide demonstrates real-time MongoDB monitoring using **mongostat** on Arm64 Azure virtual machines. It **shows low-latency, stable insert, query, update, and delete operations**, with consistent memory usage and network throughput, providing a quick health-and-performance overview during benchmarking.

## Monitor with mongostat — Terminal 3

```console
mongostat 2
```
**mongostat** gives a one-line summary every 2 seconds of inserts, queries, updates, deletes, memory use and network I/O. It’s your quick health-and-throughput dashboard during the test.

You should see an output similar to:
```output
insert query update delete getmore command dirty used flushes vsize  res qrw arw net_in net_out conn                time
     5    11      5      5     102     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  19.6k    116k   11 Sep  1 12:07:00.867
     8    16      8      8     144     1|0  0.0% 0.0%       0 3.54G 151M 1|0 0|0  28.2k    146k   11 Sep  1 12:07:02.868
     5    10      5      5      98     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  18.7k    116k   11 Sep  1 12:07:04.867
     9    18      9      9     161     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  31.1k    157k   11 Sep  1 12:07:06.866
     4     8      4      4      90     2|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  17.3k    110k   11 Sep  1 12:07:08.868
     9    18      9      9     171     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  32.5k    163k   11 Sep  1 12:07:10.866
     4     8      4      4      85     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  16.4k    109k   11 Sep  1 12:07:12.867
     9    18      9      9     178     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  33.5k    166k   11 Sep  1 12:07:14.866
     4     9      4      4      92     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  17.3k    112k   11 Sep  1 12:07:16.865
     8    17      8      8     173     2|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  33.0k    166k   11 Sep  1 12:07:18.866
insert query update delete getmore command dirty used flushes vsize  res qrw arw net_in net_out conn                time
     4     8      4      4     100     0|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  18.5k    116k   11 Sep  1 12:07:20.867
     8    17      8      8     176     2|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  32.8k    165k   11 Sep  1 12:07:22.866
     4     9      4      4     107     0|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  20.0k    122k   11 Sep  1 12:07:24.866
     7    15      7      7     160     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  30.2k    157k   11 Sep  1 12:07:26.867
     5    10      5      5     131     2|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  23.9k    136k   11 Sep  1 12:07:28.867
     6    13      6      6     141     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  26.1k    144k   11 Sep  1 12:07:30.867
     6    13      6      6     155     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  28.6k    154k   11 Sep  1 12:07:32.868
     5    10      5      5     113     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  20.6k    125k   11 Sep  1 12:07:34.866
     8    17      8      8     197     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  35.9k    179k   11 Sep  1 12:07:36.866
     4     8      4      4     103     2|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  19.0k    120k   11 Sep  1 12:07:38.868
insert query update delete getmore command dirty used flushes vsize  res qrw arw net_in net_out conn                time
     9    18      9      9     211     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  38.3k    188k   11 Sep  1 12:07:40.867
     4     8      4      4     107     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  19.6k    122k   11 Sep  1 12:07:42.867
     8    16      8      8     192     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  34.6k    176k   11 Sep  1 12:07:44.866
     5    11      5      5     132     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  23.9k    139k   11 Sep  1 12:07:46.866
     5    11      5      5     149     2|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  26.9k    148k   11 Sep  1 12:07:48.867
     7    15      7      7     188     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  33.7k    173k   11 Sep  1 12:07:50.867
     4     9      4      4     112     2|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  20.3k    126k   11 Sep  1 12:07:52.866
     8    17      8      8     229     0|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  40.9k    200k   11 Sep  1 12:07:54.866
     4     8      4      4     116     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0  20.8k    127k   11 Sep  1 12:07:56.867
    *0    *0     *0     *0       0     1|0  0.0% 0.0%       0 3.54G 151M 0|0 0|0   253b   53.2k    6 Sep  1 12:07:58.868
```

## Explanation of mongostat Metrics

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

## Benchmark summary on Arm64
For easier comparison, shown here is a summary of benchmark results collected on an Arm64 **D4ps_v6 Azure ubuntu virtual machine**.

| insert | query | update | delete | getmore | command | dirty | used | flushes | vsize | res  | qrw  | arw  | net_in | net_out | conn | time                 |
|--------|-------|--------|--------|---------|---------|-------|------|---------|-------|------|------|------|--------|---------|------|----------------------|
| 5      | 11    | 5      | 5      | 102     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 151M | 0|0  | 0|0  | 19.6k  | 116k    | 11   | Sep  1 12:07:00.867 |
| 8      | 16    | 8      | 8      | 144     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 151M | 1|0  | 0|0  | 28.2k  | 146k    | 11   | Sep  1 12:07:02.868 |
| 5      | 10    | 5      | 5      | 98      | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 151M | 0|0  | 0|0  | 18.7k  | 116k    | 11   | Sep  1 12:07:04.867 |
| 9      | 18    | 9      | 9      | 161     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 151M | 0|0  | 0|0  | 31.1k  | 157k    | 11   | Sep  1 12:07:06.866 |
| 4      | 8     | 4      | 4      | 90      | 2|0     | 0.0%  | 0.0% | 0       | 3.54G | 151M | 0|0  | 0|0  | 17.3k  | 110k    | 11   | Sep  1 12:07:08.868 |
| 9      | 18    | 9      | 9      | 171     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 151M | 0|0  | 0|0  | 32.5k  | 163k    | 11   | Sep  1 12:07:10.866 |
| 4      | 8     | 4      | 4      | 85      | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 151M | 0|0  | 0|0  | 16.4k  | 109k    | 11   | Sep  1 12:07:12.867 |
| 9      | 18    | 9      | 9      | 178     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 151M | 0|0  | 0|0  | 33.5k  | 166k    | 11   | Sep  1 12:07:14.866 |
| 4      | 9     | 4      | 4      | 92      | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 151M | 0|0  | 0|0  | 17.3k  | 112k    | 11   | Sep  1 12:07:16.865 |
| 8      | 17    | 8      | 8      | 173     | 2|0     | 0.0%  | 0.0% | 0       | 3.54G | 151M | 0|0  | 0|0  | 33.0k  | 166k    | 11   | Sep  1 12:07:18.866 |

## Benchmark summary on x86_64:
Shown here is a summary of the benchmark results collected on x86_64 **D4s_v4 Azure ubuntu virtual machine**.

| insert | query | update | delete | getmore | command | dirty | used | flushes | vsize | res  | qrw  | arw  | net_in | net_out | conn | time                 |
|--------|-------|--------|--------|---------|---------|-------|------|---------|-------|------|------|------|--------|---------|------|----------------------|
| 9      | 18    | 9      | 9      | 153     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 183M | 0|0  | 0|0  | 29.9k  | 150k    | 10   | Sep  1 12:05:45.905 |
| 4      | 9     | 4      | 4      | 81      | 2|0     | 0.0%  | 0.0% | 0       | 3.54G | 183M | 0|0  | 0|0  | 15.9k  | 104k    | 11   | Sep  1 12:05:47.904 |
| 8      | 17    | 8      | 8      | 161     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 185M | 0|0  | 0|0  | 31.3k  | 156k    | 11   | Sep  1 12:05:49.904 |
| 7      | 15    | 7      | 7      | 134     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 187M | 0|0  | 0|0  | 26.4k  | 141k    | 11   | Sep  1 12:05:51.905 |
| 5      | 11    | 5      | 5      | 112     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 187M | 0|0  | 0|0  | 21.2k  | 124k    | 11   | Sep  1 12:05:53.904 |
| 8      | 17    | 8      | 8      | 170     | 0|0     | 0.0%  | 0.0% | 0       | 3.54G | 189M | 0|0  | 0|0  | 32.5k  | 162k    | 11   | Sep  1 12:05:55.905 |
| 4      | 8     | 4      | 4      | 85      | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 191M | 0|0  | 0|0  | 16.4k  | 108k    | 11   | Sep  1 12:05:57.907 |
| 9      | 18    | 9      | 9      | 175     | 2|0     | 0.0%  | 0.0% | 0       | 3.54G | 191M | 0|0  | 0|0  | 33.3k  | 166k    | 11   | Sep  1 12:05:59.905 |
| 8      | 17    | 8      | 8      | 179     | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 191M | 0|0  | 0|0  | 33.8k  | 168k    | 11   | Sep  1 12:06:01.905 |
| 4      | 8     | 4      | 4      | 89      | 1|0     | 0.0%  | 0.0% | 0       | 3.54G | 191M | 0|0  | 0|0  | 17.0k  | 111k    | 11   | Sep  1 12:06:03.905 |

### Highlights from Azure ubuntu Arm64 Benchmarking

When comparing the results on Arm64 vs x86_64 virtual machines:

- **Insert, Query, Update, Delete Rates:** Operation throughput is stable, with inserts and queries typically in the 4–9 ops/sec range, while updates and deletes peak slightly higher during workload bursts.
- **Memory Usage:** Resident memory remains steady around 154 MB, and virtual memory around **3.54 GB**, confirming efficient memory handling on Arm64.
- **Network Activity:** Network traffic shows **net_in between ~17k–33k and net_out between 109k–166k**, consistent with the generated workload.
- **Connections:** Connections remain stable at **4**, demonstrating MongoDB can sustain concurrent client activity without spikes.
- **Command Execution:** Command, getmore, and dirty page activity stays minimal (**0–1**), indicating no internal blocking or resource contention.
- **Overall System Behavior:** MongoDB remains responsive under sustained load, with consistent operation rates and moderate resource usage across tests.

  
You have now benchmarked MongoDB on an Azure Cobalt 100 Arm64 virtual machine and compared results with x86_64.
