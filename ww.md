
# 🚀 OpenTelemetry on Arm (GCP SUSE ARM64) – Learning Path

## 🎯 Learning Outcome

Instrument and observe a cloud-native microservice running on Arm servers using OpenTelemetry, with:

* A Python Flask microservice (example workload)
* OpenTelemetry SDK + Collector
* Prometheus for metrics
* Jaeger for distributed tracing

Target Platform: **GCP SUSE ARM64 VM**

---

# 📌 Architecture

```
Flask Microservice (ARM)
        |
        | OpenTelemetry SDK
        v
OpenTelemetry Collector
   |            |
Metrics → Prometheus
Traces  → Jaeger
```

---

# Required Ports

| Service             | Port  | Purpose             |
| ------------------- | ----- | ------------------- |
| Flask App           | 8080  | Application traffic |
| Prometheus          | 9090  | Metrics UI          |
| Jaeger UI           | 16686 | Traces UI           |
| Collector Metrics   | 8889  | Prometheus scrape   |

Open these in **GCP Firewall Rules**.

---

# ✅ Step 1: Install Docker on SUSE ARM64

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

# ✅ Step 2: Install Docker Compose (Standalone)

On SUSE, install via binary:

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

```bash
mkdir ~/otel-demo
cd ~/otel-demo
```

---

# 🐍 Step 4: Create Flask Microservice (Instrumented)

Create file:

```bash
vi app.py
```

Paste:

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


# ----------------------
# Resource
# ----------------------
resource = Resource.create({
    "service.name": "flask-arm-service"
})

# ----------------------
# Tracing
# ----------------------
trace_provider = TracerProvider(resource=resource)
trace.set_tracer_provider(trace_provider)

trace_exporter = OTLPSpanExporter(endpoint="otel-collector:4317", insecure=True)
trace_provider.add_span_processor(
    BatchSpanProcessor(trace_exporter)
)

# ----------------------
# Metrics
# ----------------------
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

# ----------------------
# Flask App
# ----------------------
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

# 📦 Step 5: Create requirements.txt

```bash
nano requirements.txt
```

Paste:

```
flask
opentelemetry-api
opentelemetry-sdk
opentelemetry-exporter-otlp
opentelemetry-instrumentation-flask
```

---

# 🐳 Step 6: Create Dockerfile

```bash
nano Dockerfile
```

Paste:

```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

CMD ["python", "app.py"]
```

---

# 📡 Step 7: Create OpenTelemetry Collector Config

```bash
nano otel-collector-config.yaml
```

Paste:

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318

exporters:
  otlp:
    endpoint: jaeger:4317
    tls:
      insecure: true

  prometheus:
    endpoint: 0.0.0.0:8889

service:
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [otlp]

    metrics:
      receivers: [otlp]
      exporters: [prometheus]
```

---

# 📊 Step 8: Create Prometheus Config

```bash
nano prometheus.yml
```

Paste:

```yaml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: "otel-collector"
    static_configs:
      - targets: ["otel-collector:8889"]
```

---

# 🧱 Step 9: Create docker-compose.yml

```bash
nano docker-compose.yml
```

Paste:

```yaml
services:
  otel-demo-app:
    build: .
    ports:
      - "8080:8080"
    depends_on:
      - otel-collector

  otel-collector:
    image: otel/opentelemetry-collector-contrib:latest
    command: ["--config=/etc/otel-collector-config.yaml"]
    volumes:
      - ./otel-collector-config.yaml:/etc/otel-collector-config.yaml
    ports:
      - "4317:4317"
      - "4318:4318"
      - "8889:8889"

  jaeger:
    image: jaegertracing/all-in-one:latest
    ports:
      - "16686:16686"

  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    command:
      - "--config.file=/etc/prometheus/prometheus.yml"
    ports:
      - "9090:9090"
```

---

# ▶️ Step 10: Start the Stack

```bash
docker compose up --build -d
```

Check:

```bash
docker ps
```

---

# Generate Traffic

```bash
curl http://<VM_EXTERNAL_IP>:8080
```
Or:

for i in {1..10}; do curl http://<VM_EXTERNAL_IP>:8080; done

# Observe Metrics (Prometheus)

Open:

👉 http://<VM_EXTERNAL_IP>:9090

Try:

```
up
process_cpu_seconds_total
http_server_duration_bucket
```

---

# 🔍 Step 13: Observe Traces (Jaeger)

Open:

👉 http://<VM_EXTERNAL_IP>:16686

Service:

```
flask-arm-service
```

Click **Find Traces**

---

# ✅ Success Criteria

✔ Flask app responds
✔ Prometheus scrapes metrics
✔ Jaeger shows traces
✔ OpenTelemetry connects all components

---

# 📌 Optional Enhancements (Advanced LP)

* Add Grafana dashboards
* Add second microservice (distributed trace demo)
* Add custom business metrics
* Deploy via Kubernetes on GKE Axion

---

If you’d like, I can next:

✅ Convert this into **Markdown Learning Path format** (like your other LPs)
✅ Add **Step summaries + learning objectives**
✅ Add **Troubleshooting section** (based on issues you faced)
✅ Add **Architecture diagram ASCII + explanation**

Just say:

👉 **“Convert this into proper LP markdown with summaries”**
