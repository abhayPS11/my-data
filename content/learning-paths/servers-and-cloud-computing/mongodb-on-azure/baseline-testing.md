---
title: MongoDB Baseline Testing 
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


### Baseline testing of MongoDB
Perform baseline testing by verifying MongoDB is running, logging into the shell, executing a few test queries, and monitoring live performance. This ensures the database is functioning correctly before starting any benchmarks.

1. Verify Installation & Service Health

```console
sudo systemctl status mongod --no-pager
```
Check if MongoDB is running, confirm it’s in the process list, and see what version is installed. Expect it to be active and without errors in logs.

2. Storage & I/O Health Check

```console
ls -lh /var/lib/mongodb
fio --name=baseline --rw=randread --bs=4k --size=100M --numjobs=1 --time_based --runtime=30 --group_reporting
```
Make sure MongoDB’s data files exist and test your disk’s read speed. You want steady, consistent performance.

3. Connectivity & CRUD Sanity Check

```console
mongosh --host localhost --port 27017
```

Inside shell:

```javascript
use baselineDB
db.testCollection.insertOne({ name: "baseline-check", value: 1 })
db.testCollection.find()
db.testCollection.updateOne({ name: "baseline-check" }, { $set: { value: 2 } })
db.testCollection.deleteOne({ name: "baseline-check" })
exit

```
Log in to MongoDB, create a test database, and run Create-Read-Update-Delete operations to ensure basic functionality works fine.

4. Basic Query Performance Test

```console
mongosh --eval '
use baselineDB;
for (let i=0; i<1000; i++) { db.perf.insertOne({index:i, value:Math.random()}) };
var start = new Date();
db.perf.find({ value: { $gt: 0.5 } }).count();
print("Query Time (ms):", new Date() - start);
'
```

Insert 1000 records and time how long it takes to run a search query. You’re checking if queries run in milliseconds.

5. Index Creation Speed Test

```console
mongosh --eval '
use baselineDB;
var start = new Date();
db.perf.createIndex({ value: 1 });
print("Index Creation Time (ms):", new Date() - start);
'
```

Measure how long MongoDB takes to create an index to speed up searches. Should be fast and consistent on repeats.

6. Concurrency Smoke Test

```console
for i in {1..5}; do
  mongosh --eval 'use baselineDB; db.concurrent.insertMany([...Array(1000).keys()].map(k=>({test:k, ts:new Date()})))' &
done
wait
```

Simulate 5 users inserting data at the same time. MongoDB should handle it without crashing or throwing errors.
