---
title: Baseline Testing
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---


Since MongoDB is installed successfully on your GCP C4A Arm virtual machine, let’s now perform simple baseline testing to validate that MongoDB runs correctly and accepts connections as expected.

## MongoDB Baseline Testing (Using `mongosh`) 

1️. Connect to MongoDB:

To establish a local connection to the MongoDB instance.
```console
mongosh mongodb://127.0.0.1:27017
```

2️. Create a Test Database and Collection:

Create a database named **baselineDB** and initialize an empty test collection.
```console
use baselineDB
db.createCollection("test")
```
You should see an output similar to:

```output
test> use baselineDB
... db.createCollection("test")
...
switched to db baselineDB
```
3️. Insert 10,000 Test Documents:

Insert sample documents to simulate basic data write operations.
```javascript
for (let i = 0; i < 10000; i++) {
  db.test.insertOne({
    record: i,
    status: "new",
    timestamp: new Date()
  })
}
```
You should see an output similar to:

```output
{
  acknowledged: true,
  insertedId: ObjectId('6892dacfbd44e23df4750aa9')
}
```

4️. Read (Query) a Subset of Documents:

Fetch a few documents to verify read functionality.
```javascript
db.test.find({ status: "new" }).limit(5)
```
You should see an output similar to:

```output
[
 {
    _id: ObjectId('6892dacbbd44e23df474e39a'),
    record: 0,
    status: 'new',
    timestamp: ISODate('2025-08-06T04:32:11.090Z')
  },
  {
    _id: ObjectId('6892dacbbd44e23df474e39b'),
    record: 1,
    status: 'new',
    timestamp: ISODate('2025-08-06T04:32:11.101Z')
  },
  {
    _id: ObjectId('6892dacbbd44e23df474e39c'),
    record: 2,
    status: 'new',
    timestamp: ISODate('2025-08-06T04:32:11.103Z')
  },
  {
    _id: ObjectId('6892dacbbd44e23df474e39d'),
    record: 3,
    status: 'new',
    timestamp: ISODate('2025-08-06T04:32:11.104Z')
  },
  {
    _id: ObjectId('6892dacbbd44e23df474e39e'),
    record: 4,
    status: 'new',
    timestamp: ISODate('2025-08-06T04:32:11.106Z')
  }
]
```
5️. Update a Document:

Update a specific document's field to validate update capability.
```javascript
db.test.updateOne({ record: 100 }, { $set: { status: "processed" } })
```
You should see an output similar to:

```output
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}
```
6️. Delete a Document:

Delete a specific document to test delete functionality.
```javascript
db.test.deleteOne({ record: 100 })
```
You should see an output similar to:

```output
{ acknowledged: true, deletedCount: 1 }
baselineDB> db.test.countDocuments()
9999
```

7️. Count Total Documents:

Count total entries to confirm expected data volume.
```javascript
db.test.countDocuments()
```

8️. Measure Execution Time (Optional):

Measure how long it takes to insert documents for performance insight.
```javascript
var start = new Date()
for (let i = 0; i < 10000; i++) {
  db.test.insertOne({ sample: i })
}
print("Insert duration (ms):", new Date() - start)
```
You should see an output similar to:

```output
... for (let i = 0; i < 10000; i++) {
...   db.test.insertOne({ sample: i })
... }
... print("Insert duration (ms):", new Date() - start)
Insert duration (ms): 4427
```
9️. Clean Up (Optional):

Remove the test database and its contents.
```javascript
db.dropDatabase()
```
The above operations confirm that MongoDB is installed successfully and is functioning as expected on the GCP Arm64 environment. 

Using **mongosh**, we validated key database operations such as **insert**, **read**, **update**, **delete**, **count**, and **performanceg** that the MongoDB instance is ready for further benchmarking and production use.
