---
title: Dataflow Streaming ETL to ClickHouse
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Dataflow Streaming ETL (Pub/Sub → ClickHouse)
This guide implements the **core real-time pipeline** using:

- Existing **Pub/Sub topic and subscription**
- **Dataflow (Apache Beam)** for streaming ETL
- **ClickHouse** running on a **GCP Axion (Arm64) VM**


### Install Python 3.11 on the Axion VM
Install Python 3.11 and the required system packages

```console
sudo zypper refresh
sudo zypper install -y python311 python311-pip python311-devel gcc gcc-c++
```

Verify installation:

```console
python3.11 --version
pip3.11 --version
```

### Create a Python Virtual Environment (Recommended)
Using a virtual environment avoids dependency conflicts with the system Python.

```console
python3.11 -m venv beam-venv
source beam-venv/bin/activate
```

### Install Apache Beam with GCP Support
Install Apache Beam and the required dependencies for Dataflow:

```console
pip install --upgrade pip
pip install "apache-beam[gcp]"
pip install requests
```

Verify Beam installation:

```console
python -c "import apache_beam; print(apache_beam.__version__)"
```

### Prepare ClickHouse for Streaming Ingestion

Connect to ClickHouse on the Axion VM:

```console
clickhouse client
```

Create the database and table used by Dataflow:

```sql
CREATE DATABASE IF NOT EXISTS realtime;

CREATE TABLE IF NOT EXISTS realtime.logs
(
    event_time DateTime,
    service String,
    level String,
    message String
)
ENGINE = MergeTree
ORDER BY event_time;
```

Verify the table:

```sql
SHOW TABLES FROM realtime;
```

Verify the table:

SHOW TABLES FROM realtime;


Exit the client:

```sql
exit;
```

### Validate Pub/Sub (Before Dataflow)
Before running Dataflow, confirm that messages can be published and pulled.

**Publish a test message:**

```console
gcloud pubsub topics publish logs-topic \
  --message '{"event_time":"2025-12-30 12:00:00","service":"api","level":"INFO","message":"PRE-DATAFLOW TEST"}'
```

**Pull the message:**

```console
gcloud pubsub subscriptions pull logs-sub --limit=1 --auto-ack
```

Successful output confirms:

- Pub/Sub topic is writable
- Subscription is readable
- IAM is functioning correctly

### Create Dataflow Streaming ETL Script

Create the Dataflow pipeline file:

`vi dataflow_etl.py`


Paste the following production-ready streaming pipeline:

```json
import json
import apache_beam as beam
from apache_beam.options.pipeline_options import PipelineOptions

PROJECT_ID = "imperial-time-463411-q5"
SUBSCRIPTION = "projects/imperial-time-463411-q5/subscriptions/logs-sub"
CLICKHOUSE_URL = "http://10.128.0.26:8123"

class ParseMessage(beam.DoFn):
    def process(self, element):
        yield json.loads(element.decode("utf-8"))

class WriteToClickHouse(beam.DoFn):
    def process(self, element):
        import requests
        row = (
            f"{element['event_time']}\t"
            f"{element['service']}\t"
            f"{element['level']}\t"
            f"{element['message']}\n"
        )
        requests.post(
            CLICKHOUSE_URL,
            data=row,
            headers={"Content-Type": "text/tab-separated-values"},
            params={"query": "INSERT INTO realtime.logs FORMAT TabSeparated"}
        )

options = PipelineOptions(
    streaming=True,
    save_main_session=True
)

with beam.Pipeline(options=options) as p:
    (
        p
        | "Read from PubSub" >> beam.io.ReadFromPubSub(subscription=SUBSCRIPTION)
        | "Parse JSON" >> beam.ParDo(ParseMessage())
        | "Write to ClickHouse" >> beam.ParDo(WriteToClickHouse())
    )
```

### Run the Dataflow Streaming Job
Execute the pipeline from the Axion VM:

```console
python3.11 dataflow_etl.py \
  --runner=DataflowRunner \
  --project=imperial-time-463411-q5 \
  --region=us-central1 \
  --temp_location=gs://imperial-time-463411-q5-dataflow-temp/temp \
  --streaming
```

Expected behavior:

```output
Job state becomes RUNNING
Dataflow creates 1+ worker VMs
Streaming Engine autoscaling is enabled
```

### End-to-End Validation
Publish live streaming data

```console
gcloud pubsub topics publish logs-topic \
  --message '{"event_time":"2025-12-30 13:30:00","service":"api","level":"INFO","message":"FRESH DATAFLOW WORKING"}'
```

Verify data in ClickHouse

```sql
SELECT *
FROM realtime.logs
ORDER BY event_time DESC
LIMIT 5;
```

Expected output:

```output
┌──────────event_time─┬─service─┬─level─┬─message────────────────────────┐
│ 2025-12-30 13:30:00 │ api     │ INFO  │ FRESH DATAFLOW WORKING         │
└─────────────────────┴─────────┴───────┴────────────────────────────────┘
````

This confirms:
- Dataflow is consuming Pub/Sub
- Dataflow can reach ClickHouse over HTTP
- Real-time ingestion is functional
