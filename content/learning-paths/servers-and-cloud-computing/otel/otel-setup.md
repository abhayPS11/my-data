# 🚀 OpenTelemetry on Arm (GCP SUSE ARM64) – Learning Path (Part 1: Environment & Application Setup)

---

## 🎯 Learning Objective

Prepare an ARM64 SUSE VM with container tooling and deploy an instrumented Python Flask microservice that emits OpenTelemetry traces and metrics.

---

## 📌 Architecture Scope (Part 1)

```
Flask Microservice (ARM)
        |
        | OpenTelemetry SDK
        v
OpenTelemetry Collector
```

---

## 🔓 Required Firewall Port

| Service   | Port | Purpose             |
| --------- | ---- | ------------------- |
| Flask App | 8080 | Application traffic |

---

# ✅ Step 1: Install Docker on SUSE ARM64

### Summary

Installs Docker Engine to run ARM-based containers.

```bash
sudo zypper refresh
sudo zypper install -y docker
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
newgrp docker
```

Verify:

```bash
docker --version
```

---

# ✅ Step 2: Install Docker Compose

### Summary

Installs Docker Compose v2 binary for multi-container orchestration.

```bash
sudo curl -L https://github.com/docker/compose/releases/download/v2.27.0/docker-compose-linux-aarch64 \
  -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```

Verify:

```bash
docker-compose --version
```

---

# 📁 Step 3: Create Project Directory

### Summary

Creates workspace for OpenTelemetry demo.

```bash
mkdir ~/otel-demo
cd ~/otel-demo
```

---

# 🐍 Step 4: Create Instrumented Flask Application

### Summary

Creates a Flask service integrated with OpenTelemetry SDK for tracing and metrics.

```bash
vi app.py
```

### 📄 File: app.py

```python
from flask import Flask
import time

from opentelemetry import trace, metrics
from opentelemetry.sdk.resources import Resource
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.metrics import MeterProvider
from opentelemetry.sdk.metrics.export import PeriodicExportingMetricReader
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.exporter.otlp.proto.grpc.metric_exporter import OTLPMetricExporter
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.sdk.trace.export import BatchSpanProcessor

resource = Resource.create({
    "service.name": "flask-arm-service"
})

trace_provider = TracerProvider(resource=resource)
trace.set_tracer_provider(trace_provider)

trace_exporter = OTLPSpanExporter(endpoint="otel-collector:4317", insecure=True)
trace_provider.add_span_processor(
    BatchSpanProcessor(trace_exporter)
)

metric_exporter = OTLPMetricExporter(endpoint="otel-collector:4317", insecure=True)

metric_reader = PeriodicExportingMetricReader(
    metric_exporter,
    export_interval_millis=5000
)

meter_provider = MeterProvider(
    resource=resource,
    metric_readers=[metric_reader]
)

metrics.set_meter_provider(meter_provider)

meter = metrics.get_meter(__name__)

request_counter = meter.create_counter(
    name="demo_requests_total",
    description="Total number of requests"
)

app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)

@app.route("/")
def hello():
    request_counter.add(1)
    time.sleep(0.2)
    return "Hello OpenTelemetry!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8080)
```

---

# 📦 Step 5: Create Python Dependencies

### Summary

Defines required libraries for Flask and OpenTelemetry.

```bash
nano requirements.txt
```

### 📄 File: requirements.txt

```
flask
opentelemetry-api
opentelemetry-sdk
opentelemetry-exporter-otlp
opentelemetry-instrumentation-flask
```

---

# 🐳 Step 6: Create Application Dockerfile

### Summary

Builds ARM-compatible container image for Flask app.

```bash
nano Dockerfile
```

### 📄 File: Dockerfile

```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

CMD ["python", "app.py"]
```

---

## ✅ End of Part 1

You now have:

✔ Docker & Compose installed on ARM64
✔ Instrumented Flask service
✔ Container build setup

➡️ Proceed to **Part 2: Observability Stack & Telemetry Pipeline**
