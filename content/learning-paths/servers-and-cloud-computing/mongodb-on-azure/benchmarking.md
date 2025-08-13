---
title: MongoDB Benchmarking
weight: 7

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## MongoDB Benchmarking by (**mongotop** and **mongostat**)

This guide will help the user measure MongoDB’s performance in real time.
The user will install the official MongoDB database tools, start MongoDB, run a script to simulate heavy load, and watch the database’s live performance using **mongotop** and **mongostat**.

1. Install MongoDB Database Tools

```console
wget https://fastdl.mongodb.org/tools/db/mongodb-database-tools-rhel93-aarch64-100.12.1.tgz
tar -xzf mongodb-database-tools-rhel93-aarch64-100.12.1.tgz
echo "export PATH=\$PATH:/root/mongodb-database-tools-rhel93-aarch64-100.12.1/bin" >> ~/.bashrc
source ~/.bashrc
```
These commands download and unpack MongoDB’s official monitoring tools (**mongotop** & **mongostat**), then add them to your PATH so they work from any terminal.

2. Verify the Installation

```console
mongotop --version
mongostat --version
```
This checks that both tools were installed correctly and are ready to use.

You should see an output similar to:
```output
mongotop version: 100.12.1
git version: 6ed2d84211f54467fd6495a381a3e7102f48ba42
Go version: go1.23.8
   os: linux
   arch: arm64
   compiler: gc
mongostat version: 100.12.1
git version: 6ed2d84211f54467fd6495a381a3e7102f48ba42
Go version: go1.23.8
   os: linux
   arch: arm64
   compiler: gc
```

3. Start MongoDB Server

```console
mkdir -p /data/db
mongod --dbpath /data/db --bind_ip_all --logpath /var/log/mongodb.log
```
These commands create a folder for MongoDB’s data, then start the database server in the background, allowing connections from any IP, and save logs for troubleshooting.

4. Create a Long-Running Load Script for Benchmarking

Save this script file as **long_system_load.js**:

```javascript
function randomString(len) {
    return Math.random().toString(36).substring(2, 2 + len);
}

var systemCollections = [
    { db: "admin", coll: "atlascli" },
    { db: "config", coll: "system_sessions_bench" },
    { db: "config", coll: "transactions_bench" },
    { db: "local", coll: "system_replset_bench" },
    { db: "benchmarkDB", coll: "testCollection" },
    { db: "benchmarkDB", coll: "cursorTest" },
    { db: "test", coll: "atlascli" },
    { db: "test", coll: "system_sessions_bench" },
    { db: "test", coll: "admin_system_version_test" }
];

systemCollections.forEach(function(ns) {
    let col = db.getSiblingDB(ns.db).getCollection(ns.coll);
    col.drop();
    for (let i = 0; i < 100; i++) {
        col.insertOne({ rnd: randomString(10), ts: new Date(), idx: i });
    }
    col.findOne();
});

var totalCycles = 200;   
var pauseMs = 1000;      

for (let cycle = 0; cycle < totalCycles; cycle++) {
    systemCollections.forEach(function(ns) {
        let col = db.getSiblingDB(ns.db).getCollection(ns.coll);

        col.insertOne({ cycle, action: "insert", value: randomString(8), ts: new Date() });
        col.find({ cycle: { $lte: cycle } }).limit(10).toArray();
        col.updateMany({}, { $set: { updatedAt: new Date() } });
        col.deleteMany({ idx: { $gt: 80 } });

        let cursor = col.find().batchSize(5);
        while (cursor.hasNext()) {
            cursor.next();
        }
    });

    print(`Cycle ${cycle + 1} / ${totalCycles} completed`);
    sleep(pauseMs);
}

print("=== Long load generation completed ===");
```

This is the load generator script, it creates several collections and repeatedly **inserts, queries, updates** and **deletes** data. Running it simulates real application traffic so the monitors have something to measure.

{{% notice Note %}}
Before proceeding, the load script and the monitoring tools must be run in separate terminals simultaneously.The load script continuously generates activity in MongoDB, keeping the database busy with multiple operations.
The mongotop and mongostat tools monitor and report this activity in real time as it happens.If all commands are run in the same terminal, the monitoring tools will only start after the script finishes, preventing real-time observation of MongoDB’s performance.
{{% /notice %}}

### Run the load script (start the workload) — Terminal A

```console
mongosh < long_system_load.js
```

This tells the MongoDB shell to execute the whole script. The script will run its cycles and print progress; while it runs it generates read/write activity on the server.

You should see an output similar to:
```output
test> // long_system_load.js

test> // Run with: mongosh < long_system_load.js

test>

test> function randomString(len) {
...     return Math.random().toString(36).substring(2, 2 + len);
... }
[Function: randomString]
test>

test> // ---------- 1. Safe shadow "system-like" namespaces ----------

test> var systemCollections = [
...     { db: "admin", coll: "atlascli" },
...     { db: "config", coll: "system_sessions_bench" },
...     { db: "config", coll: "transactions_bench" },
...     { db: "local", coll: "system_replset_bench" },
...     { db: "benchmarkDB", coll: "testCollection" },
...     { db: "benchmarkDB", coll: "cursorTest" },
...     { db: "test", coll: "atlascli" },
...     { db: "test", coll: "system_sessions_bench" },
...     { db: "test", coll: "admin_system_version_test" }
... ];

test>

test> // Create and warm up

test> systemCollections.forEach(function(ns) {
...     let col = db.getSiblingDB(ns.db).getCollection(ns.coll);
...     col.drop();
...     for (let i = 0; i < 100; i++) {
...         col.insertOne({ rnd: randomString(10), ts: new Date(), idx: i });
...     }
...     col.findOne();
... });

test>

test> // ---------- 2. Generate load loop ----------

test> var totalCycles = 200;   // increase this for longer runs

test> var pauseMs = 1000;      // 1 second pause between cycles

test>

test> for (let cycle = 0; cycle < totalCycles; cycle++) {
...     systemCollections.forEach(function(ns) {
...         let col = db.getSiblingDB(ns.db).getCollection(ns.coll);
...
...         col.insertOne({ cycle, action: "insert", value: randomString(8), ts: new Date() });
...         col.find({ cycle: { $lte: cycle } }).limit(10).toArray();
...         col.updateMany({}, { $set: { updatedAt: new Date() } });
...         col.deleteMany({ idx: { $gt: 80 } });
...
...         let cursor = col.find().batchSize(5);
...         while (cursor.hasNext()) {
...             cursor.next();
...         }
...     });
...
...     print(`Cycle ${cycle + 1} / ${totalCycles} completed`);
...     sleep(pauseMs);
... }
Cycle 1 / 200 completed
Cycle 2 / 200 completed
Cycle 3 / 200 completed
Cycle 4 / 200 completed
Cycle 5 / 200 completed
Cycle 6 / 200 completed
Cycle 7 / 200 completed
Cycle 8 / 200 completed
Cycle 9 / 200 completed
Cycle 10 / 200 completed
Cycle 11 / 200 completed
Cycle 12 / 200 completed
Cycle 13 / 200 completed
Cycle 14 / 200 completed
Cycle 15 / 200 completed
Cycle 16 / 200 completed
Cycle 17 / 200 completed
Cycle 18 / 200 completed
Cycle 19 / 200 completed
Cycle 20 / 200 completed
Cycle 21 / 200 completed
Cycle 22 / 200 completed
Cycle 23 / 200 completed
Cycle 24 / 200 completed
Cycle 25 / 200 completed
Cycle 26 / 200 completed
Cycle 27 / 200 completed
Cycle 28 / 200 completed
Cycle 29 / 200 completed
Cycle 30 / 200 completed
Cycle 31 / 200 completed
Cycle 32 / 200 completed
Cycle 33 / 200 completed
Cycle 34 / 200 completed
Cycle 35 / 200 completed
Cycle 36 / 200 completed
Cycle 37 / 200 completed
Cycle 38 / 200 completed
Cycle 39 / 200 completed
Cycle 40 / 200 completed
Cycle 41 / 200 completed
Cycle 42 / 200 completed
Cycle 43 / 200 completed
Cycle 44 / 200 completed
Cycle 45 / 200 completed
Cycle 46 / 200 completed
Cycle 47 / 200 completed
Cycle 48 / 200 completed
Cycle 49 / 200 completed
Cycle 50 / 200 completed
Cycle 51 / 200 completed
Cycle 52 / 200 completed
Cycle 53 / 200 completed
Cycle 54 / 200 completed
Cycle 55 / 200 completed
Cycle 56 / 200 completed
Cycle 57 / 200 completed
Cycle 58 / 200 completed
Cycle 59 / 200 completed
Cycle 60 / 200 completed
Cycle 61 / 200 completed
Cycle 62 / 200 completed
Cycle 63 / 200 completed
Cycle 64 / 200 completed
Cycle 65 / 200 completed
Cycle 66 / 200 completed
Cycle 67 / 200 completed
Cycle 68 / 200 completed
Cycle 69 / 200 completed
Cycle 70 / 200 completed
Cycle 71 / 200 completed
Cycle 72 / 200 completed
Cycle 73 / 200 completed
Cycle 74 / 200 completed
Cycle 75 / 200 completed
Cycle 76 / 200 completed
Cycle 77 / 200 completed
Cycle 78 / 200 completed
Cycle 79 / 200 completed
Cycle 80 / 200 completed
Cycle 81 / 200 completed
Cycle 82 / 200 completed
Cycle 83 / 200 completed
Cycle 84 / 200 completed
Cycle 85 / 200 completed
Cycle 86 / 200 completed
Cycle 87 / 200 completed
Cycle 88 / 200 completed
Cycle 89 / 200 completed
Cycle 90 / 200 completed
Cycle 91 / 200 completed
Cycle 92 / 200 completed
Cycle 93 / 200 completed
Cycle 94 / 200 completed
Cycle 95 / 200 completed
Cycle 96 / 200 completed
Cycle 97 / 200 completed
Cycle 98 / 200 completed
Cycle 99 / 200 completed
Cycle 100 / 200 completed
Cycle 101 / 200 completed
Cycle 102 / 200 completed
Cycle 103 / 200 completed
Cycle 104 / 200 completed
Cycle 105 / 200 completed
Cycle 106 / 200 completed
Cycle 107 / 200 completed
Cycle 108 / 200 completed
Cycle 109 / 200 completed
Cycle 110 / 200 completed
Cycle 111 / 200 completed
Cycle 112 / 200 completed
Cycle 113 / 200 completed
Cycle 114 / 200 completed
Cycle 115 / 200 completed
Cycle 116 / 200 completed
Cycle 117 / 200 completed
Cycle 118 / 200 completed
Cycle 119 / 200 completed
Cycle 120 / 200 completed
Cycle 121 / 200 completed
Cycle 122 / 200 completed
Cycle 123 / 200 completed
Cycle 124 / 200 completed
Cycle 125 / 200 completed
Cycle 126 / 200 completed
Cycle 127 / 200 completed
Cycle 128 / 200 completed
Cycle 129 / 200 completed
Cycle 130 / 200 completed
Cycle 131 / 200 completed
Cycle 132 / 200 completed
Cycle 133 / 200 completed
Cycle 134 / 200 completed
Cycle 135 / 200 completed
Cycle 136 / 200 completed
Cycle 137 / 200 completed
Cycle 138 / 200 completed
Cycle 139 / 200 completed
Cycle 140 / 200 completed
Cycle 141 / 200 completed
Cycle 142 / 200 completed
Cycle 143 / 200 completed
Cycle 144 / 200 completed
Cycle 145 / 200 completed
Cycle 146 / 200 completed
Cycle 147 / 200 completed
Cycle 148 / 200 completed
Cycle 149 / 200 completed
Cycle 150 / 200 completed
Cycle 151 / 200 completed
Cycle 152 / 200 completed
Cycle 153 / 200 completed
Cycle 154 / 200 completed
Cycle 155 / 200 completed
Cycle 156 / 200 completed
Cycle 157 / 200 completed
Cycle 158 / 200 completed
Cycle 159 / 200 completed
Cycle 160 / 200 completed
Cycle 161 / 200 completed
Cycle 162 / 200 completed
Cycle 163 / 200 completed
Cycle 164 / 200 completed
Cycle 165 / 200 completed
Cycle 166 / 200 completed
Cycle 167 / 200 completed
Cycle 168 / 200 completed
Cycle 169 / 200 completed
Cycle 170 / 200 completed
Cycle 171 / 200 completed
Cycle 172 / 200 completed
Cycle 173 / 200 completed
Cycle 174 / 200 completed
Cycle 175 / 200 completed
Cycle 176 / 200 completed
Cycle 177 / 200 completed
Cycle 178 / 200 completed
Cycle 179 / 200 completed
Cycle 180 / 200 completed
Cycle 181 / 200 completed
Cycle 182 / 200 completed
Cycle 183 / 200 completed
Cycle 184 / 200 completed
Cycle 185 / 200 completed
Cycle 186 / 200 completed
Cycle 187 / 200 completed
Cycle 188 / 200 completed
Cycle 189 / 200 completed
Cycle 190 / 200 completed
Cycle 191 / 200 completed
Cycle 192 / 200 completed
Cycle 193 / 200 completed
Cycle 194 / 200 completed
Cycle 195 / 200 completed
Cycle 196 / 200 completed
Cycle 197 / 200 completed
Cycle 198 / 200 completed
Cycle 199 / 200 completed
Cycle 200 / 200 completed

test>

test> print("=== Long load generation completed ===");
=== Long load generation completed ===
```
Now you can proceed with monitoring:

- **mongotop** to observe activity per collection.
- **mongostat** to monitor overall operations per second, memory usage, and network activity.

### Run mongotop (monitor per-collection I/O) — Terminal B

```console
mongotop 2
```
**mongotop** shows how much time the server spends reading and writing each collection (refreshes every 2 seconds here). It helps you see which collections are busiest and whether reads or writes dominate.

You should see an output similar to:
```output
                admin.atlascli      1ms     0ms      1ms
        benchmarkDB.cursorTest      1ms     0ms      0ms
    benchmarkDB.testCollection      1ms     0ms      0ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
    local.system_replset_bench      1ms     0ms      0ms
test.admin_system_version_test      1ms     0ms      0ms
                 test.atlascli      1ms     0ms      1ms
    test.system_sessions_bench      1ms     0ms      0ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:52:41Z
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:52:43Z
                admin.atlascli      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
        benchmarkDB.cursorTest      1ms     0ms      0ms
    benchmarkDB.testCollection      1ms     0ms      0ms
    local.system_replset_bench      1ms     0ms      1ms
test.admin_system_version_test      1ms     0ms      0ms
                 test.atlascli      1ms     0ms      0ms
    test.system_sessions_bench      1ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:52:45Z
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      1ms
                admin.atlascli      1ms     0ms      1ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:52:47Z
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
test.admin_system_version_test      1ms     0ms      1ms
    test.system_sessions_bench      1ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:52:49Z
test.admin_system_version_test      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
                 test.atlascli      2ms     0ms      1ms
                admin.atlascli      1ms     0ms      1ms
        benchmarkDB.cursorTest      1ms     0ms      1ms
    benchmarkDB.testCollection      1ms     0ms      1ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
    local.system_replset_bench      1ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:52:51Z
                admin.atlascli      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:52:53Z
                admin.atlascli      1ms     0ms      1ms
        benchmarkDB.cursorTest      1ms     0ms      0ms
    benchmarkDB.testCollection      1ms     0ms      1ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
    local.system_replset_bench      1ms     0ms      1ms
test.admin_system_version_test      1ms     0ms      1ms
                 test.atlascli      1ms     0ms      1ms
    test.system_sessions_bench      1ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:52:55Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:52:57Z
                admin.atlascli      2ms     0ms      1ms
    test.system_sessions_bench      2ms     0ms      1ms
        benchmarkDB.cursorTest      1ms     0ms      1ms
    benchmarkDB.testCollection      1ms     0ms      0ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
    local.system_replset_bench      1ms     0ms      1ms
test.admin_system_version_test      1ms     0ms      1ms
                 test.atlascli      1ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:52:59Z
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

                            ns    total    read    write    2025-08-13T06:53:01Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     0ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     0ms      1ms
                 test.atlascli      2ms     0ms      1ms
    test.system_sessions_bench      2ms     0ms      1ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:03Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:05Z
                admin.atlascli      2ms     0ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
    local.system_replset_bench      2ms     0ms      1ms
test.admin_system_version_test      2ms     0ms      1ms
                 test.atlascli      2ms     0ms      1ms
    test.system_sessions_bench      2ms     0ms      1ms
  config.system_sessions_bench      1ms     0ms      1ms
     config.transactions_bench      1ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:07Z
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

                            ns    total    read    write    2025-08-13T06:53:09Z
                admin.atlascli      2ms     0ms      1ms
        benchmarkDB.cursorTest      2ms     0ms      1ms
    benchmarkDB.testCollection      2ms     0ms      1ms
    local.system_replset_bench      2ms     0ms      1ms
test.admin_system_version_test      2ms     0ms      1ms
                 test.atlascli      2ms     0ms      1ms
    test.system_sessions_bench      2ms     0ms      1ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:11Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:13Z
                admin.atlascli      2ms     0ms      1ms
        benchmarkDB.cursorTest      2ms     0ms      1ms
    benchmarkDB.testCollection      2ms     0ms      1ms
    local.system_replset_bench      2ms     0ms      1ms
test.admin_system_version_test      2ms     0ms      1ms
                 test.atlascli      2ms     0ms      1ms
    test.system_sessions_bench      2ms     0ms      1ms
  config.system_sessions_bench      1ms     0ms      0ms
     config.transactions_bench      1ms     0ms      0ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:15Z
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:17Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     0ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     0ms      1ms
test.admin_system_version_test      2ms     0ms      1ms
                 test.atlascli      2ms     0ms      1ms
    test.system_sessions_bench      2ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:19Z
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     1ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:21Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     0ms      1ms
    benchmarkDB.testCollection      2ms     0ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     0ms      1ms
test.admin_system_version_test      2ms     0ms      1ms
                 test.atlascli      2ms     0ms      1ms
    test.system_sessions_bench      2ms     0ms      1ms
     config.transactions_bench      1ms     0ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:23Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      2ms
     config.transactions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:25Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     0ms      1ms
     config.transactions_bench      2ms     0ms      1ms
    local.system_replset_bench      2ms     0ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:27Z
                admin.atlascli      5ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     1ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:29Z
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

                            ns    total    read    write    2025-08-13T06:53:31Z
                admin.atlascli      5ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
                 test.atlascli      4ms     1ms      2ms
test.admin_system_version_test      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:33Z
test.admin_system_version_test      5ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:35Z
                admin.atlascli      5ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:37Z
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
                admin.atlascli      2ms     1ms      1ms
  config.system_sessions_bench      2ms     0ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:39Z
                admin.atlascli      5ms     2ms      2ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     0ms      1ms
    local.system_replset_bench      2ms     0ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:41Z
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     1ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
                admin.atlascli      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:43Z
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     0ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:45Z
                admin.atlascli      5ms     2ms      2ms
test.admin_system_version_test      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
    test.system_sessions_bench      5ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:47Z
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

                            ns    total    read    write    2025-08-13T06:53:49Z
                admin.atlascli      5ms     2ms      3ms
        benchmarkDB.cursorTest      5ms     2ms      3ms
    benchmarkDB.testCollection      5ms     2ms      2ms
  config.system_sessions_bench      5ms     2ms      2ms
     config.transactions_bench      5ms     2ms      2ms
    local.system_replset_bench      5ms     2ms      2ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:51Z
test.admin_system_version_test      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
    test.system_sessions_bench      5ms     2ms      2ms
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:53Z
                admin.atlascli      5ms     2ms      3ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:55Z
        benchmarkDB.cursorTest      5ms     2ms      2ms
    benchmarkDB.testCollection      5ms     2ms      2ms
    local.system_replset_bench      5ms     2ms      2ms
test.admin_system_version_test      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      3ms
    test.system_sessions_bench      5ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
                admin.atlascli      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:53:57Z
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

                            ns    total    read    write    2025-08-13T06:53:59Z
                admin.atlascli      5ms     2ms      3ms
        benchmarkDB.cursorTest      5ms     2ms      3ms
    benchmarkDB.testCollection      5ms     2ms      3ms
    local.system_replset_bench      5ms     2ms      3ms
                 test.atlascli      5ms     2ms      3ms
    test.system_sessions_bench      5ms     2ms      3ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
test.admin_system_version_test      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:01Z
test.admin_system_version_test      5ms     2ms      2ms
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:03Z
                admin.atlascli      6ms     2ms      3ms
  config.system_sessions_bench      5ms     2ms      2ms
     config.transactions_bench      4ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:05Z
        benchmarkDB.cursorTest      6ms     2ms      3ms
                 test.atlascli      6ms     2ms      3ms
    benchmarkDB.testCollection      5ms     2ms      3ms
    local.system_replset_bench      5ms     2ms      3ms
test.admin_system_version_test      5ms     2ms      3ms
    test.system_sessions_bench      5ms     2ms      3ms
                admin.atlascli      3ms     1ms      1ms
     config.transactions_bench      3ms     2ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:07Z
                admin.atlascli      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:09Z
                admin.atlascli      5ms     2ms      3ms
        benchmarkDB.cursorTest      5ms     2ms      3ms
    benchmarkDB.testCollection      5ms     2ms      3ms
  config.system_sessions_bench      5ms     2ms      2ms
     config.transactions_bench      5ms     2ms      2ms
    local.system_replset_bench      5ms     2ms      3ms
                 test.atlascli      5ms     2ms      3ms
    test.system_sessions_bench      5ms     1ms      3ms
test.admin_system_version_test      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:11Z
test.admin_system_version_test      5ms     2ms      3ms
    test.system_sessions_bench      3ms     1ms      1ms
                admin.atlascli      2ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
    benchmarkDB.testCollection      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:13Z
                admin.atlascli      5ms     2ms      3ms
    benchmarkDB.testCollection      3ms     1ms      1ms
        benchmarkDB.cursorTest      2ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
    local.system_replset_bench      2ms     1ms      1ms
test.admin_system_version_test      2ms     1ms      1ms
                 test.atlascli      2ms     1ms      1ms
    test.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:15Z
        benchmarkDB.cursorTest      6ms     2ms      3ms
test.admin_system_version_test      6ms     2ms      3ms
                 test.atlascli      6ms     2ms      3ms
    test.system_sessions_bench      6ms     2ms      3ms
    benchmarkDB.testCollection      5ms     2ms      3ms
  config.system_sessions_bench      5ms     2ms      2ms
     config.transactions_bench      5ms     2ms      2ms
    local.system_replset_bench      5ms     2ms      3ms
                admin.atlascli      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:17Z
                admin.atlascli      3ms     1ms      1ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:19Z
                admin.atlascli      6ms     3ms      3ms
  config.system_sessions_bench      6ms     3ms      3ms
     config.transactions_bench      6ms     2ms      3ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
    local.system_replset_bench      3ms     2ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:21Z
        benchmarkDB.cursorTest      6ms     3ms      3ms
    benchmarkDB.testCollection      6ms     3ms      3ms
    local.system_replset_bench      6ms     3ms      3ms
test.admin_system_version_test      6ms     3ms      3ms
                 test.atlascli      6ms     3ms      3ms
    test.system_sessions_bench      6ms     3ms      3ms
                admin.atlascli      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:23Z
                admin.atlascli      3ms     1ms      1ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:25Z
                admin.atlascli      6ms     3ms      3ms
  config.system_sessions_bench      6ms     3ms      3ms
    local.system_replset_bench      6ms     2ms      3ms
     config.transactions_bench      5ms     2ms      3ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:27Z
        benchmarkDB.cursorTest      6ms     2ms      3ms
    benchmarkDB.testCollection      6ms     2ms      3ms
test.admin_system_version_test      6ms     2ms      3ms
                 test.atlascli      6ms     2ms      3ms
    test.system_sessions_bench      6ms     2ms      3ms
                admin.atlascli      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:29Z
                admin.atlascli      3ms     1ms      1ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:31Z
                admin.atlascli      6ms     3ms      3ms
    local.system_replset_bench      6ms     2ms      3ms
    benchmarkDB.testCollection      5ms     1ms      3ms
  config.system_sessions_bench      5ms     2ms      3ms
     config.transactions_bench      5ms     2ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:33Z
        benchmarkDB.cursorTest      6ms     3ms      3ms
test.admin_system_version_test      6ms     3ms      3ms
                 test.atlascli      6ms     2ms      3ms
    test.system_sessions_bench      6ms     2ms      3ms
    benchmarkDB.testCollection      4ms     3ms      1ms
                admin.atlascli      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:35Z
                admin.atlascli      3ms     1ms      1ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:37Z
                admin.atlascli      6ms     3ms      3ms
  config.system_sessions_bench      6ms     3ms      3ms
     config.transactions_bench      6ms     3ms      3ms
    local.system_replset_bench      6ms     3ms      3ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:39Z
        benchmarkDB.cursorTest      7ms     3ms      3ms
    benchmarkDB.testCollection      6ms     3ms      3ms
test.admin_system_version_test      6ms     2ms      3ms
                 test.atlascli      6ms     2ms      3ms
    test.system_sessions_bench      6ms     2ms      3ms
                admin.atlascli      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:41Z
                admin.atlascli      3ms     1ms      1ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:43Z
                admin.atlascli      6ms     3ms      3ms
  config.system_sessions_bench      5ms     2ms      3ms
     config.transactions_bench      5ms     2ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:45Z
        benchmarkDB.cursorTest      6ms     3ms      3ms
    benchmarkDB.testCollection      6ms     3ms      3ms
    local.system_replset_bench      6ms     2ms      3ms
test.admin_system_version_test      6ms     3ms      3ms
                 test.atlascli      6ms     2ms      3ms
    test.system_sessions_bench      6ms     3ms      3ms
                admin.atlascli      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:47Z
                admin.atlascli      3ms     1ms      1ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
  config.system_sessions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:49Z
                admin.atlascli      6ms     2ms      3ms
  config.system_sessions_bench      5ms     2ms      3ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      2ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:51Z
    test.system_sessions_bench      7ms     3ms      3ms
        benchmarkDB.cursorTest      6ms     2ms      4ms
    benchmarkDB.testCollection      6ms     2ms      3ms
     config.transactions_bench      6ms     3ms      3ms
    local.system_replset_bench      6ms     3ms      3ms
test.admin_system_version_test      6ms     3ms      3ms
                 test.atlascli      6ms     3ms      3ms
                admin.atlascli      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:53Z
                admin.atlascli      3ms     1ms      1ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:55Z
                admin.atlascli      6ms     2ms      3ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:57Z
        benchmarkDB.cursorTest      7ms     3ms      4ms
                 test.atlascli      7ms     3ms      3ms
    test.system_sessions_bench      7ms     3ms      4ms
    benchmarkDB.testCollection      6ms     3ms      3ms
  config.system_sessions_bench      6ms     2ms      3ms
     config.transactions_bench      6ms     3ms      3ms
    local.system_replset_bench      6ms     3ms      3ms
test.admin_system_version_test      5ms     1ms      3ms
                admin.atlascli      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:54:59Z
test.admin_system_version_test      4ms     2ms      1ms
                admin.atlascli      3ms     1ms      1ms
        benchmarkDB.cursorTest      3ms     1ms      1ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:01Z
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:03Z
                admin.atlascli      7ms     3ms      3ms
        benchmarkDB.cursorTest      7ms     3ms      4ms
    benchmarkDB.testCollection      7ms     3ms      3ms
    local.system_replset_bench      7ms     3ms      4ms
  config.system_sessions_bench      6ms     3ms      3ms
     config.transactions_bench      6ms     3ms      3ms
                 test.atlascli      5ms     1ms      3ms
test.admin_system_version_test      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:05Z
    test.system_sessions_bench      7ms     3ms      3ms
test.admin_system_version_test      6ms     3ms      3ms
                 test.atlascli      4ms     2ms      1ms
                admin.atlascli      3ms     1ms      1ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:07Z
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:09Z
                admin.atlascli      7ms     3ms      4ms
  config.system_sessions_bench      6ms     3ms      3ms
     config.transactions_bench      6ms     3ms      3ms
    local.system_replset_bench      6ms     2ms      4ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:11Z
        benchmarkDB.cursorTest      7ms     3ms      3ms
    benchmarkDB.testCollection      7ms     3ms      4ms
test.admin_system_version_test      7ms     3ms      4ms
                 test.atlascli      7ms     3ms      3ms
    test.system_sessions_bench      7ms     3ms      3ms
    local.system_replset_bench      4ms     2ms      2ms
                admin.atlascli      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:13Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:15Z
                admin.atlascli      5ms     1ms      4ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:17Z
        benchmarkDB.cursorTest      7ms     3ms      4ms
    benchmarkDB.testCollection      7ms     3ms      3ms
    local.system_replset_bench      7ms     3ms      4ms
                 test.atlascli      7ms     3ms      4ms
  config.system_sessions_bench      6ms     3ms      3ms
     config.transactions_bench      6ms     3ms      3ms
    test.system_sessions_bench      6ms     2ms      3ms
                admin.atlascli      4ms     2ms      2ms
test.admin_system_version_test      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:19Z
test.admin_system_version_test      7ms     3ms      4ms
    test.system_sessions_bench      4ms     2ms      1ms
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:21Z
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:23Z
                admin.atlascli      7ms     3ms      4ms
  config.system_sessions_bench      6ms     3ms      3ms
     config.transactions_bench      6ms     2ms      3ms
    local.system_replset_bench      6ms     2ms      3ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:25Z
        benchmarkDB.cursorTest      7ms     3ms      4ms
    benchmarkDB.testCollection      7ms     3ms      4ms
test.admin_system_version_test      7ms     3ms      4ms
                 test.atlascli      7ms     3ms      4ms
    test.system_sessions_bench      7ms     3ms      4ms
    local.system_replset_bench      4ms     2ms      1ms
                admin.atlascli      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:27Z
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:29Z
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:31Z
                admin.atlascli      7ms     3ms      4ms
        benchmarkDB.cursorTest      7ms     2ms      4ms
    benchmarkDB.testCollection      7ms     3ms      4ms
    local.system_replset_bench      7ms     3ms      4ms
  config.system_sessions_bench      6ms     3ms      3ms
     config.transactions_bench      6ms     3ms      3ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:33Z
test.admin_system_version_test      7ms     3ms      4ms
                 test.atlascli      7ms     3ms      4ms
    test.system_sessions_bench      7ms     3ms      4ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
                admin.atlascli      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:35Z
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:37Z
                admin.atlascli      7ms     3ms      4ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      1ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:39Z
        benchmarkDB.cursorTest      7ms     3ms      4ms
    benchmarkDB.testCollection      7ms     3ms      4ms
    local.system_replset_bench      7ms     3ms      4ms
                 test.atlascli      7ms     3ms      4ms
  config.system_sessions_bench      6ms     3ms      3ms
     config.transactions_bench      6ms     3ms      3ms
                admin.atlascli      4ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:41Z
test.admin_system_version_test      7ms     3ms      4ms
    test.system_sessions_bench      7ms     3ms      4ms
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:43Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:45Z
                admin.atlascli      7ms     3ms      4ms
  config.system_sessions_bench      6ms     2ms      3ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:47Z
        benchmarkDB.cursorTest      8ms     3ms      4ms
    benchmarkDB.testCollection      8ms     3ms      4ms
    local.system_replset_bench      8ms     3ms      4ms
     config.transactions_bench      7ms     3ms      3ms
                 test.atlascli      7ms     3ms      4ms
    test.system_sessions_bench      6ms     2ms      4ms
  config.system_sessions_bench      4ms     2ms      1ms
                admin.atlascli      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:49Z
test.admin_system_version_test      7ms     3ms      4ms
    test.system_sessions_bench      5ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
                admin.atlascli      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:51Z
                admin.atlascli      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:53Z
                admin.atlascli      6ms     2ms      4ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:55Z
        benchmarkDB.cursorTest      7ms     3ms      4ms
    benchmarkDB.testCollection      7ms     3ms      4ms
  config.system_sessions_bench      7ms     3ms      3ms
     config.transactions_bench      7ms     3ms      3ms
    local.system_replset_bench      7ms     3ms      4ms
                admin.atlascli      5ms     3ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:57Z
test.admin_system_version_test      8ms     3ms      4ms
                 test.atlascli      8ms     3ms      4ms
    test.system_sessions_bench      7ms     3ms      4ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
                admin.atlascli      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:55:59Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:01Z
        config.system.sessions     11ms     0ms     11ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
                admin.atlascli      3ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms

                            ns    total    read    write    2025-08-13T06:56:03Z
                admin.atlascli      8ms     3ms      4ms
    local.system_replset_bench      8ms     3ms      4ms
  config.system_sessions_bench      7ms     3ms      3ms
     config.transactions_bench      7ms     3ms      3ms
    benchmarkDB.testCollection      6ms     2ms      4ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
test.admin_system_version_test      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:05Z
        benchmarkDB.cursorTest      8ms     3ms      4ms
                 test.atlascli      8ms     3ms      4ms
test.admin_system_version_test      7ms     3ms      4ms
    test.system_sessions_bench      7ms     3ms      4ms
    benchmarkDB.testCollection      5ms     2ms      2ms
                admin.atlascli      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:07Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:09Z
                admin.atlascli      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
                 test.atlascli      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:11Z
                admin.atlascli      8ms     3ms      4ms
  config.system_sessions_bench      6ms     2ms      3ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
     config.transactions_bench      3ms     1ms      1ms
                 test.atlascli      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:13Z
    benchmarkDB.testCollection      8ms     3ms      4ms
    local.system_replset_bench      8ms     3ms      4ms
        benchmarkDB.cursorTest      7ms     3ms      4ms
     config.transactions_bench      7ms     3ms      3ms
                 test.atlascli      7ms     2ms      4ms
                admin.atlascli      4ms     1ms      2ms
  config.system_sessions_bench      4ms     2ms      1ms
test.admin_system_version_test      3ms     1ms      2ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:15Z
test.admin_system_version_test      8ms     3ms      4ms
    test.system_sessions_bench      7ms     3ms      4ms
                 test.atlascli      5ms     2ms      2ms
                admin.atlascli      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
        benchmarkDB.cursorTest      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:17Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
test.admin_system_version_test      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:19Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:21Z
                admin.atlascli      8ms     3ms      4ms
  config.system_sessions_bench      7ms     3ms      3ms
     config.transactions_bench      7ms     3ms      3ms
    local.system_replset_bench      7ms     2ms      4ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:23Z
        benchmarkDB.cursorTest      8ms     3ms      4ms
    benchmarkDB.testCollection      8ms     3ms      4ms
                 test.atlascli      8ms     3ms      4ms
    test.system_sessions_bench      7ms     2ms      4ms
    local.system_replset_bench      5ms     3ms      2ms
                admin.atlascli      4ms     1ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
  config.system_sessions_bench      3ms     1ms      2ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:25Z
test.admin_system_version_test      8ms     3ms      4ms
    test.system_sessions_bench      5ms     2ms      2ms
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    local.system_replset_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:27Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    benchmarkDB.testCollection      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:29Z
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
                admin.atlascli      3ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
    test.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:31Z
                admin.atlascli      8ms     3ms      5ms
  config.system_sessions_bench      7ms     3ms      3ms
     config.transactions_bench      6ms     2ms      3ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:33Z
        benchmarkDB.cursorTest      8ms     3ms      5ms
    benchmarkDB.testCollection      8ms     3ms      4ms
    local.system_replset_bench      8ms     3ms      5ms
                 test.atlascli      8ms     2ms      5ms
                admin.atlascli      4ms     1ms      2ms
     config.transactions_bench      4ms     2ms      1ms
test.admin_system_version_test      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:35Z
test.admin_system_version_test      8ms     3ms      4ms
    test.system_sessions_bench      8ms     3ms      4ms
                 test.atlascli      5ms     2ms      2ms
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:37Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:39Z
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:41Z
                admin.atlascli      8ms     3ms      5ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      2ms
     config.transactions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:43Z
    benchmarkDB.testCollection      8ms     3ms      4ms
     config.transactions_bench      8ms     4ms      4ms
    local.system_replset_bench      8ms     3ms      5ms
  config.system_sessions_bench      7ms     3ms      4ms
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:45Z
        benchmarkDB.cursorTest      8ms     3ms      4ms
test.admin_system_version_test      8ms     3ms      4ms
                 test.atlascli      8ms     3ms      5ms
    test.system_sessions_bench      8ms     3ms      4ms
                admin.atlascli      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      1ms
     config.transactions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:47Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      2ms
     config.transactions_bench      3ms     1ms      1ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:49Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
  config.system_sessions_bench      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
     config.transactions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:51Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
  config.system_sessions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:53Z
                admin.atlascli      9ms     4ms      5ms
  config.system_sessions_bench      8ms     3ms      4ms
     config.transactions_bench      7ms     3ms      4ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:55Z
        benchmarkDB.cursorTest      9ms     4ms      5ms
    benchmarkDB.testCollection      9ms     4ms      5ms
    local.system_replset_bench      9ms     4ms      5ms
                 test.atlascli      7ms     2ms      4ms
     config.transactions_bench      5ms     2ms      2ms
                admin.atlascli      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:57Z
test.admin_system_version_test      9ms     4ms      5ms
    test.system_sessions_bench      9ms     4ms      5ms
                 test.atlascli      6ms     3ms      2ms
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     1ms      2ms
     config.transactions_bench      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:56:59Z
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     1ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:01Z
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:03Z
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
  config.system_sessions_bench      4ms     1ms      2ms
     config.transactions_bench      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:05Z
                admin.atlascli      9ms     3ms      5ms
  config.system_sessions_bench      7ms     3ms      4ms
        benchmarkDB.cursorTest      4ms     1ms      2ms
    benchmarkDB.testCollection      4ms     1ms      2ms
    local.system_replset_bench      4ms     1ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     1ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      3ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:07Z
    benchmarkDB.testCollection      9ms     4ms      5ms
    local.system_replset_bench      9ms     3ms      5ms
     config.transactions_bench      8ms     4ms      4ms
                admin.atlascli      4ms     1ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:09Z
        benchmarkDB.cursorTest      9ms     4ms      5ms
                 test.atlascli      9ms     4ms      5ms
    test.system_sessions_bench      9ms     4ms      5ms
test.admin_system_version_test      8ms     3ms      5ms
                admin.atlascli      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:11Z
test.admin_system_version_test      5ms     3ms      2ms
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:13Z
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

                            ns    total    read    write    2025-08-13T06:57:15Z
                 test.atlascli      5ms     2ms      2ms
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:17Z
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:19Z
                admin.atlascli      9ms     4ms      5ms
  config.system_sessions_bench      8ms     4ms      4ms
     config.transactions_bench      8ms     4ms      4ms
    local.system_replset_bench      8ms     2ms      5ms
test.admin_system_version_test      5ms     2ms      3ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:21Z
        benchmarkDB.cursorTest      9ms     4ms      5ms
    benchmarkDB.testCollection      9ms     4ms      5ms
                 test.atlascli      8ms     2ms      5ms
    local.system_replset_bench      6ms     4ms      2ms
                admin.atlascli      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:23Z
test.admin_system_version_test      9ms     4ms      5ms
    test.system_sessions_bench      9ms     4ms      5ms
                 test.atlascli      6ms     3ms      2ms
        benchmarkDB.cursorTest      5ms     2ms      2ms
    benchmarkDB.testCollection      5ms     2ms      2ms
                admin.atlascli      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:25Z
        benchmarkDB.cursorTest      5ms     2ms      2ms
                admin.atlascli      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:27Z
                admin.atlascli      4ms     2ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     1ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:29Z
                admin.atlascli      5ms     2ms      3ms
        benchmarkDB.cursorTest      5ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     1ms      2ms
                 test.atlascli      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:31Z
        benchmarkDB.cursorTest      5ms     2ms      2ms
test.admin_system_version_test      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
                admin.atlascli      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
    test.system_sessions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:33Z
                admin.atlascli     10ms     4ms      5ms
  config.system_sessions_bench      9ms     4ms      4ms
     config.transactions_bench      7ms     3ms      4ms
        benchmarkDB.cursorTest      5ms     2ms      2ms
    benchmarkDB.testCollection      5ms     2ms      2ms
    local.system_replset_bench      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
    test.system_sessions_bench      5ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:35Z
    benchmarkDB.testCollection     10ms     4ms      5ms
    local.system_replset_bench     10ms     4ms      5ms
     config.transactions_bench      6ms     3ms      2ms
                admin.atlascli      5ms     2ms      2ms
        benchmarkDB.cursorTest      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
    test.system_sessions_bench      5ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
test.admin_system_version_test      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:37Z
        benchmarkDB.cursorTest      9ms     4ms      5ms
                 test.atlascli      9ms     4ms      5ms
    test.system_sessions_bench      9ms     3ms      5ms
                admin.atlascli      5ms     2ms      3ms
    benchmarkDB.testCollection      5ms     2ms      2ms
    local.system_replset_bench      5ms     2ms      2ms
test.admin_system_version_test      5ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:39Z
test.admin_system_version_test      9ms     4ms      5ms
                admin.atlascli      5ms     2ms      2ms
    test.system_sessions_bench      5ms     3ms      2ms
        benchmarkDB.cursorTest      4ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
    local.system_replset_bench      4ms     2ms      2ms
                 test.atlascli      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms

                            ns    total    read    write    2025-08-13T06:57:41Z
                admin.atlascli      5ms     2ms      2ms
        benchmarkDB.cursorTest      5ms     2ms      2ms
    local.system_replset_bench      5ms     2ms      2ms
test.admin_system_version_test      5ms     2ms      2ms
                 test.atlascli      5ms     2ms      2ms
    test.system_sessions_bench      5ms     2ms      2ms
    benchmarkDB.testCollection      4ms     2ms      2ms
  config.system_sessions_bench      4ms     2ms      2ms
     config.transactions_bench      4ms     2ms      2ms
          admin.system.version      0ms     0ms      0ms
```
