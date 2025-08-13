---
title: MongoDB Benchmarking with mongostat
weight: 9

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Monitoring MongoDB Performance
This guide demonstrates real-time MongoDB monitoring using **mongostat** on Arm64 Azure virtual machines. It **shows low-latency, stable insert, query, update, and delete operations**, with consistent memory usage and network throughput, providing a quick health-and-performance overview during benchmarking.

### Monitor with mongostat — Terminal 3

```console
mongostat 2
```
**mongostat** gives a one-line summary every 2 seconds of inserts, queries, updates, deletes, memory use and network I/O. It’s your quick health-and-throughput dashboard during the test.

You should see an output similar to:
```output
insert query update delete getmore command dirty used flushes vsize  res qrw arw net_in net_out conn                time
     8    17      8      8     151     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  29.1k    145k    8 Aug 13 08:20:30.608
     4     9      4      4      91     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  17.7k    109k    8 Aug 13 08:20:32.609
     9    18      9      9     162     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  31.2k    156k    8 Aug 13 08:20:34.608
     4     9      4      4      85     1|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  16.4k    105k    8 Aug 13 08:20:36.608
     8    17      8      8     170     1|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  32.5k    160k    8 Aug 13 08:20:38.609
     4     9      4      4      85     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  16.3k    106k    8 Aug 13 08:20:40.607
     8    17      8      8     175     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  33.1k    163k    8 Aug 13 08:20:42.608
     4     9      4      4      90     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  16.9k    108k    8 Aug 13 08:20:44.607
     8    17      8      8     179     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  33.8k    166k    8 Aug 13 08:20:46.607
     4     8      4      4      89     1|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  17.1k    110k    8 Aug 13 08:20:48.609
insert query update delete getmore command dirty used flushes vsize  res qrw arw net_in net_out conn                time
     9    18      9      9     189     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  35.0k    170k    8 Aug 13 08:20:50.608
     4     8      4      4      94     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  17.5k    111k    8 Aug 13 08:20:52.609
     9    18      9      9     189     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  35.0k    173k    8 Aug 13 08:20:54.609
     4     9      4      4      99     1|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  18.2k    113k    8 Aug 13 08:20:56.608
     8    17      8      8     197     1|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  36.5k    177k    8 Aug 13 08:20:58.609
     4     9      6      4      99     1|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  18.2k    115k    8 Aug 13 08:21:00.608
     9    18      9      9     202     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  37.0k    180k    8 Aug 13 08:21:02.607
     4     8      4      4     103     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  18.8k    116k    8 Aug 13 08:21:04.608
     9    18      9      9     207     1|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  37.7k    184k    8 Aug 13 08:21:06.607
     4     8      4      4     103     1|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  19.0k    118k    8 Aug 13 08:21:08.608
insert query update delete getmore command dirty used flushes vsize  res qrw arw net_in net_out conn                time
     8    16      8      8     188     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  33.9k    170k    8 Aug 13 08:21:10.608
     5    10      5      5     135     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  24.5k    137k    8 Aug 13 08:21:12.608
     7    13      7      7     156     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  28.5k    152k    8 Aug 13 08:21:14.607
     6    13      6      6     172     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  30.6k    160k    8 Aug 13 08:21:16.607
     4     9      4      4     119     1|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  21.7k    127k    8 Aug 13 08:21:18.608
     8    17      8      8     217     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  38.8k    190k    8 Aug 13 08:21:20.608
     4     9      4      4     112     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  20.1k    124k    8 Aug 13 08:21:22.607
     8    17      8      8     233     0|0  0.0% 0.0%       0 3.53G 154M 0|0 0|0  41.4k    199k    8 Aug 13 08:21:24.609
```

{{% notice Note %}} Benchmarking was performed in both an Azure Linux 3.0 Docker container and an Azure Linux 3.0 virtual machine. The benchmark results were found to be relatively stable. {{% /notice %}}

Accordingly, this Learning path includes benchmark results from virtual machines only, for both x86 and Arm64 platforms. 

### Benchmark summary on x86_64:
The following benchmark results are collected on an x86_64 **D4s_v4 Azure virtual machine using the Azure Linux 3.0 image published by Ntegral Inc**.

| insert | query | update | delete | getmore | command | dirty | used | flushes | vsize  | res   | qrw | arw | net_in | net_out | conn   | time                   |
|--------|-------|--------|--------|---------|---------|-------|------|---------|--------|-------|-----|-----|--------|---------|--------|------------------------|
| 4      | 8     | 4      | 4      | 76      | 1|0     | 0.0%  | 0.0% | 0       | 2.54G  | 145M  | 0|0 | 0|0 | 15.1k | 99.2k  | 10 Aug 13 10:16:38.605 |
| 8      | 17    | 8      | 8      | 152     | 1|0     | 0.0%  | 0.0% | 0       | 2.54G  | 145M  | 0|0 | 0|0 | 30.1k | 148k   | 11 Aug 13 10:16:40.606 |
| 4      | 9     | 4      | 4      | 76      | 1|0     | 0.0%  | 0.0% | 0       | 2.54G  | 146M  | 0|0 | 0|0 | 15.0k | 102k   | 11 Aug 13 10:16:42.604 |
| 8      | 17    | 8      | 8      | 161     | 1|0     | 0.0%  | 0.0% | 0       | 2.54G  | 146M  | 0|0 | 0|0 | 31.2k | 154k   | 11 Aug 13 10:16:44.606 |
| 4      | 8     | 4      | 4      | 80      | 1|0     | 0.0%  | 0.0% | 0       | 2.54G  | 146M  | 0|0 | 0|0 | 15.7k | 105k   | 11 Aug 13 10:16:46.607 |
| 8      | 17    | 8      | 8      | 150     | 2|0     | 0.0%  | 0.0% | 0       | 2.54G  | 146M  | 0|0 | 0|0 | 29.2k | 151k   | 11 Aug 13 10:16:48.605 |
| 4      | 9     | 4      | 4      | 96      | 1|0     | 0.0%  | 0.0% | 0       | 2.54G  | 146M  | 0|0 | 0|0 | 18.6k | 114k   | 11 Aug 13 10:16:50.606 |
| 7      | 15    | 7      | 7      | 138     | 1|0     | 0.0%  | 0.0% | 0       | 2.54G  | 147M  | 0|0 | 0|0 | 26.6k | 141k   | 11 Aug 13 10:16:52.603 |
| 5      | 11    | 5      | 5      | 117     | 1|0     | 0.0%  | 0.0% | 0       | 2.54G  | 147M  | 0|0 | 0|0 | 22.3k | 128k   | 11 Aug 13 10:16:54.605 |
| 6      | 12    | 6      | 6      | 141     | 1|0     | 0.0%  | 0.0% | 0       | 2.54G  | 148M  | 0|0 | 0|0 | 25.6k | 142k   | 11 Aug 13 10:17:12.605 |


### Benchmark summary on Arm64
The following benchmark results are collected on an Arm64 **D4ps_v6 Azure virtual machine created from the Azure Linux 3.0 custom image using the AArch64 ISO**.

| insert | query | update | delete | getmore | command | dirty | used | flushes | vsize | res  | qrw | arw | net_in | net_out | conn | time |
|--------|-------|--------|--------|---------|---------|-------|------|---------|-------|------|-----|-----|--------|---------|------|------|
| 8      | 17    | 8      | 8      | 151     | 0|0    | 0.0%  | 0.0% | 0       | 3.53G | 154M | 0|0 | 0|0 | 29.1k  | 145k    | 8    | Aug 13 08:20:30.608 |
| 4      | 9     | 4      | 4      | 91      | 0|0    | 0.0%  | 0.0% | 0       | 3.53G | 154M | 0|0 | 0|0 | 17.7k  | 109k    | 8    | Aug 13 08:20:32.609 |
| 9      | 18    | 9      | 9      | 162     | 0|0    | 0.0%  | 0.0% | 0       | 3.53G | 154M | 0|0 | 0|0 | 31.2k  | 156k    | 8    | Aug 13 08:20:34.608 |
| 4      | 9     | 4      | 4      | 85      | 1|0    | 0.0%  | 0.0% | 0       | 3.53G | 154M | 0|0 | 0|0 | 16.4k  | 105k    | 8    | Aug 13 08:20:36.608 |
| 8      | 17    | 8      | 8      | 170     | 1|0    | 0.0%  | 0.0% | 0       | 3.53G | 154M | 0|0 | 0|0 | 32.5k  | 160k    | 8    | Aug 13 08:20:38.609 |
| 4      | 9     | 4      | 4      | 85      | 0|0    | 0.0%  | 0.0% | 0       | 3.53G | 154M | 0|0 | 0|0 | 16.3k  | 106k    | 8    | Aug 13 08:20:40.607 |
| 8      | 17    | 8      | 8      | 175     | 0|0    | 0.0%  | 0.0% | 0       | 3.53G | 154M | 0|0 | 0|0 | 33.1k  | 163k    | 8    | Aug 13 08:20:42.608 |
| 4      | 9     | 4      | 4      | 90      | 0|0    | 0.0%  | 0.0% | 0       | 3.53G | 154M | 0|0 | 0|0 | 16.9k  | 108k    | 8    | Aug 13 08:20:44.607 |
| 8      | 17    | 8      | 8      | 179     | 0|0    | 0.0%  | 0.0% | 0       | 3.53G | 154M | 0|0 | 0|0 | 33.8k  | 166k    | 8    | Aug 13 08:20:46.607 |
| 4      | 8     | 4      | 4      | 89      | 1|0    | 0.0%  | 0.0% | 0       | 3.53G | 154M | 0|0 | 0|0 | 17.1k  | 110k    | 8    | Aug 13 08:20:48.609 |

### Highlights from Azure Linux Arm64 Benchmarking

- **Insert, Query, Update, Delete Rates:** Operations per second are fairly stable, with inserts and queries usually ranging between 4–9 ops/sec for light collections, and updates/deletes slightly higher during load cycles.
- **Memory Usage:** Resident memory (res) stays consistent around 154 MB, while virtual memory (vsize) is about 3.53 GB. This shows MongoDB is efficiently managing memory on Arm64.
- **Network Activity:** Network in/out fluctuates between ~17k–41k (net_in) and 105k–199k (net_out), reflecting the workload generated by the script.
- **Connections:** Connection count is stable at 8, showing MongoDB handles multiple simultaneous connections without spikes.
- **Command Execution:** Commands, getmore operations, and dirty pages are low (mostly 0–1), indicating MongoDB is not getting blocked by internal tasks.
- **Overall System Behavior:** The database remains responsive under heavy load, as seen by consistent operation rates and moderate resource usage.
