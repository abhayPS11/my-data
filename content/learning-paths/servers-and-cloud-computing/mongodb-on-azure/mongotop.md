---
title: MongoDB Benchmarking with mongotop
weight: 8

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Monitoring MongoDB Performance
This guide demonstrates how to monitor MongoDB performance using **mongotop**, showing **read/write** activity across collections in **real time**. It includes benchmark results collected on Azure Arm64 virtual machines, providing a reference for expected latencies.

### Run mongotop — Terminal 2

```console
mongotop 2
```
**mongotop** shows how much time the server spends reading and writing each collection (refreshes every 2 seconds here). It helps you see which collections are busiest and whether reads or writes dominate.

You should see an output similar to:
```output
                          ns    total    read    write    2025-09-01T11:08:45Z
              admin.atlascli      3ms     1ms      2ms
      benchmarkDB.cursorTest      3ms     1ms      2ms
  benchmarkDB.testCollection      3ms     1ms      2ms
config.system_sessions_bench      3ms     1ms      2ms
   config.transactions_bench      3ms     1ms      2ms
  local.system_replset_bench      3ms     1ms      2ms
               test.atlascli      3ms     1ms      2ms
        admin.system.version      0ms     0ms      0ms
      config.system.sessions      0ms     0ms      0ms
         config.transactions      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:08:47Z
test.admin_system_version_test      3ms     1ms      1ms
                admin.atlascli      1ms     0ms      1ms
        benchmarkDB.cursorTest      1ms     0ms      0ms
    benchmarkDB.testCollection      1ms     0ms      0ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
    local.system_replset_bench      1ms     0ms      0ms
                 test.atlascli      1ms     0ms      1ms
    test.system_sessions_bench      1ms     0ms      0ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:08:49Z
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:08:51Z
                admin.atlascli      2ms     0ms      1ms
        benchmarkDB.cursorTest      1ms     0ms      1ms
    benchmarkDB.testCollection      1ms     0ms      0ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
    local.system_replset_bench      1ms     0ms      1ms
test.admin_system_version_test      1ms     0ms      0ms
                 test.atlascli      1ms     0ms      1ms
    test.system_sessions_bench      1ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:08:53Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:08:55Z
                admin.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     0ms      1ms
        benchmarkDB.cursorTest      1ms     0ms      1ms
    benchmarkDB.testCollection      1ms     0ms      1ms
  config.system_sessions_bench      1ms     0ms      1ms
     config.transactions_bench      1ms     0ms      0ms
    local.system_replset_bench      1ms     0ms      1ms
test.admin_system_version_test      1ms     0ms      1ms
                 test.atlascli      1ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:08:57Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:08:59Z
                admin.atlascli      2ms     1ms      1ms
test.admin_system_version_test      2ms     0ms      1ms
    test.system_sessions_bench      2ms     0ms      1ms
        benchmarkDB.cursorTest      1ms     0ms      1ms
    benchmarkDB.testCollection      1ms     0ms      1ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
    local.system_replset_bench      1ms     0ms      1ms
                 test.atlascli      1ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:01Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:03Z
                admin.atlascli      2ms     0ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     0ms      1ms
    local.system_replset_bench      2ms     0ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:05Z
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:07Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     0ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     0ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:09Z
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:11Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:13Z
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:15Z
                admin.atlascli      2ms     0ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     0ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     0ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     0ms      1ms
    test.system_sessions_bench      2ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:17Z
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:19Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:21Z
                admin.atlascli      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
test.admin_system_version_test      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:23Z
test.admin_system_version_test      5ms     2ms      2ms
    test.system_sessions_bench      3ms     2ms      1ms
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:25Z
                admin.atlascli      5ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
                 test.atlascli      3ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:27Z
        benchmarkDB.cursorTest      5ms     2ms      2ms
    benchmarkDB.testCollection      5ms     2ms      2ms
test.admin_system_version_test      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
    test.system_sessions_bench      5ms     2ms      2ms
    local.system_replset_bench      3ms     2ms      1ms
                admin.atlascli      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:29Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:31Z
test.admin_system_version_test      5ms     2ms      2ms
    test.system_sessions_bench      5ms     2ms      2ms
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:33Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:35Z
                admin.atlascli      5ms     2ms      2ms
        benchmarkDB.cursorTest      5ms     2ms      2ms
    benchmarkDB.testCollection      5ms     2ms      2ms
test.admin_system_version_test      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
    test.system_sessions_bench      5ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:37Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:39Z
                admin.atlascli      5ms     2ms      3ms
    local.system_replset_bench      5ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:41Z
        benchmarkDB.cursorTest      5ms     2ms      2ms
test.admin_system_version_test      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
    test.system_sessions_bench      5ms     2ms      2ms
    benchmarkDB.testCollection      3ms     1ms      1ms
                admin.atlascli      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:43Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:45Z
                admin.atlascli      5ms     2ms      3ms
        benchmarkDB.cursorTest      5ms     2ms      3ms
    benchmarkDB.testCollection      5ms     2ms      2ms
    local.system_replset_bench      5ms     2ms      2ms
test.admin_system_version_test      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
    test.system_sessions_bench      5ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:47Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-09-01T11:09:49Z
                admin.atlascli      5ms     2ms      3ms
        benchmarkDB.cursorTest      5ms     2ms      2ms
    benchmarkDB.testCollection      5ms     2ms      2ms
    local.system_replset_bench      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
test.admin_system_version_test      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms
```

{{% notice Note %}}
Benchmarking was performed in both an Azure Linux 3.0 Docker container and an Azure Linux 3.0 virtual machine. The benchmark results were found to be relatively stable.
{{% /notice %}}

Accordingly, this Learning path includes benchmark results from virtual machines only, for both x86 and Arm64 platforms.

### Benchmark summary on x86_64:
The following benchmark results are collected on an x86_64 **D4s_v4 Azure virtual machine using the Azure Linux 3.0 image published by Ntegral Inc**.

| Namespace (ns) | Total Time Range | Read Time Range | Write Time Range | Notes |
| :------------------------------- | :--------------- | :-------------- | :--------------- | :------------------------------------------------------------------------------------------------------------------------------- |
| admin.atlascli | 2–7 ms | 0–2 ms | 1–4 ms | Admin CLI operations. |
| benchmarkDB.cursorTest | 2–7 ms | 0–3 ms | 1–4 ms | Secondary benchmark load. |
| benchmarkDB.testCollection | 2–6 ms | 0–2 ms | 1–3 ms | Main benchmark workload. |
| config.system_sessions_bench | 1–5 ms | 0–2 ms | 1–3 ms | System/benchmark sessions. |
| config.transactions_bench | 2–6 ms | 0–2 ms | 1–4 ms | Internal transaction benchmark. |
| local.system_replset_bench | 1–6 ms | 0–2 ms | 1–3 ms | Local replica set benchmark. |
| test.admin_system_version_test | 2–5 ms | 0–2 ms | 1–3 ms | Light version check activity. |
| test.atlascli | 1–6 ms | 0–2 ms | 1–3 ms | CLI/system background operations. |
| test.system_sessions_bench | 2–6 ms | 0–2 ms | 1–3 ms | System/benchmark sessions (test namespace). |
| admin.system.version | 0–0 ms | 0–0 ms | 0–0 ms | Appears to be inactive or have instantaneous responses. |


### Benchmark summary on Arm64:
The following benchmark results were collected on an Arm64 **D4ps_v6 Azure virtual machine created from a custom Azure Linux 3.0 image using the AArch64 ISO**.

| Namespace (ns)                  | Total Time Range | Read Time Range | Write Time Range | Notes |
| :------------------------------- | :--------------- | :-------------- | :--------------- | :------------------------------------------------------------------------------------------------------------------------------- |
| admin.atlascli                   | 1–5 ms           | 0–2 ms          | 1–3 ms           | Admin CLI operations. |
| benchmarkDB.cursorTest           | 1–5 ms           | 0–2 ms          | 0–3 ms           | Secondary benchmark load. |
| benchmarkDB.testCollection       | 1–5 ms           | 0–2 ms          | 0–2 ms           | Main benchmark workload. |
| config.system_sessions_bench     | 1–4 ms           | 0–2 ms          | 0–2 ms           | System/benchmark sessions. |
| config.transactions_bench        | 1–4 ms           | 0–2 ms          | 0–2 ms           | Internal transaction benchmark. |
| local.system_replset_bench       | 1–5 ms           | 0–2 ms          | 0–2 ms           | Local replica set benchmark. |
| test.admin_system_version_test   | 1–5 ms           | 0–2 ms          | 0–2 ms           | Light version check activity. |
| test.atlascli                    | 1–5 ms           | 0–2 ms          | 0–2 ms           | CLI/system background operations. |
| test.system_sessions_bench       | 1–5 ms           | 0–2 ms          | 0–2 ms           | System/benchmark sessions (test namespace). |
| admin.system.version             | 0–0 ms           | 0–0 ms          | 0–0 ms           | Appears to be inactive or have instantaneous responses. |

### Highlights from Azure Linux ARM64 Benchmarking

- **Most active namespaces:** admin.atlascli, benchmarkDB.testCollection, benchmarkDB.cursorTest, and test.atlascli — total times 1–5ms.
- **Read patterns:** Reads across collections are 0–2ms, showing low-latency performance on Arm64.
- **Write patterns:** Writes are mostly 1–3ms, indicating a balanced workload on Arm64.
- **Less active namespaces:** config.system_sessions_bench, config.transactions_bench, test.system_sessions_bench — total 1–5ms.
- **Idle collections:** admin.system.version remains 0ms, showing minimal activity.
- **Overall observation:** MongoDB operations on Arm64 are lightweight with consistently low-latency reads and writes, confirming efficient Arm64 performance.
