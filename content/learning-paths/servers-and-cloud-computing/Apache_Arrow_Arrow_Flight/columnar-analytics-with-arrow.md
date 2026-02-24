---
title: Columnar Analytics with Apache Arrow on Arm64
weight: 2

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Columnar Analytics with Apache Arrow

In this section, you use **Apache Arrow’s columnar execution engine to read and write analytical datasets stored in MinIO (S3)**. You will work with **Parquet and ORC formats** and explore predicate pushdown and column pruning, which are key performance optimizations in modern analytics engines.

## Architecture Overview

```text
Python Analytics Scripts
        |
        v
Apache Arrow Dataset API
        |
        v
Parquet / ORC Columnar Files
        |
        v
MinIO (S3 Object Storage)
```

## Write Parquet Data to MinIO

Create a dataset and write it in Parquet format. Create a file `write_parquet.py`.

```python
import pyarrow as pa
import pyarrow.parquet as pq
import s3fs

table = pa.table({
    "id": list(range(1000)),
    "value": [i * 10 for i in range(1000)]
})

fs = s3fs.S3FileSystem(
    key="minioadmin",
    secret="minioadmin",
    client_kwargs={"endpoint_url": "http://127.0.0.1:9000"}
)

pq.write_table(table, "arrow-data/dataset.parquet", filesystem=fs)
```

### Run it

```bash
python write_parquet.py
```

The output is similar to:
```output
Parquet file written to MinIO
```

### Verify in MinIO UI

Open bucket arrow-data → you should see:

```test
dataset.parquet
```

## Read Parquet Using Arrow Dataset API

Create a file `read_parquet.py`.

```python
import pyarrow.dataset as ds
import s3fs

fs = s3fs.S3FileSystem(
    key="minioadmin",
    secret="minioadmin",
    client_kwargs={"endpoint_url": "http://127.0.0.1:9000"}
)

dataset = ds.dataset(
    "arrow-data/dataset.parquet",
    format="parquet",
    filesystem=fs
)

table = dataset.to_table()
print(table.schema)
print("Rows:", table.num_rows)
```

### Run it

```bash
python read_parquet.py
```

The output is similar to:
```output
id: int64
value: int64
Rows: 1000
```

## Predicate Pushdown & Column Pruning

Create file `filter_parquet.py`

```python
import pyarrow.dataset as ds
import s3fs

fs = s3fs.S3FileSystem(
    key="minioadmin",
    secret="minioadmin",
    client_kwargs={"endpoint_url": "http://127.0.0.1:9000"}
)

dataset = ds.dataset(
    "arrow-data/dataset.parquet",
    format="parquet",
    filesystem=fs
)

filtered = dataset.to_table(
    filter=ds.field("id") > 990,
    columns=["id"]
)

print(filtered)
```

## Run it

```bash
python filter_parquet.py
```

**This confirms:**

- Predicate pushdown
- Column pruning
- Vectorized execution

The output is similar to:
```output
pyarrow.Table
id: int64
----
id: [[991,992,993,994,995,996,997,998,999]]
```

## Write ORC Data to MinIO

Create a file `write_orc.py`.

```pyhton
import pyarrow as pa
import pyarrow.orc as orc
import s3fs

table = pa.table({
    "id": list(range(1000)),
    "value": [i * 10 for i in range(1000)]
})

fs = s3fs.S3FileSystem(
    key="minioadmin",
    secret="minioadmin",
    client_kwargs={"endpoint_url": "http://127.0.0.1:9000"}
)

with fs.open("arrow-data/dataset.orc", "wb") as f:
    orc.write_table(table, f)

print("ORC file written to MinIO")
```

## Run it

```bash
python write_orc.py
```

The output is similar to:
```output
ORC file written to MinIO
```

**Verify in MinIO UI:**

- **dataset.orc** appears next to Parquet

## What You Have Accomplished

- Used Parquet and ORC on Arm64
- Performed vectorized analytics with Arrow
- Leveraged dataset pushdown optimizations
- Built a cloud-native analytics storage layer

## What’s Next

In the next section, you will enable high-speed memory-to-memory analytics using Apache Arrow Flight
