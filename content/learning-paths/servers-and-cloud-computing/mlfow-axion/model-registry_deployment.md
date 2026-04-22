---
title: Deploy MLflow on GCP SUSE Arm64 (Model Registry & Serving)
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Model Registry and Deployment

This section covers model versioning, alias assignment, and serving the model as an API.


## Terminal usage

- **Terminal A** → Model operations, testing  
- **Terminal B** → MLflow server (already running)  
- **Optional Terminal C** → Model serving (optional)

## Set tracking URI

```bash
export MLFLOW_TRACKING_URI=http://127.0.0.1:5000
```

## Create alias script

```bash
cd ~/mlflow-learning-path/demo
```

```bash
cat > set_prod.py <<'EOF'
import os
from mlflow import MlflowClient

tracking_uri = os.environ.get("MLFLOW_TRACKING_URI", "http://127.0.0.1:5000")
client = MlflowClient(tracking_uri=tracking_uri)

versions = client.search_model_versions("name='iris-model'")

best_v = None
best_acc = -1

for v in versions:
    run = client.get_run(v.run_id)
    acc = run.data.metrics.get("accuracy", -1)
    if acc > best_acc:
        best_acc = acc
        best_v = v.version

client.set_registered_model_alias("iris-model", "production", best_v)
print("Production version:", best_v)
EOF
```

## Assign production model

```bash
python set_prod.py
```

## Serve model

You can run this in **Terminal A** or open **Terminal C**.

```bash
cd ~/mlflow-learning-path
source mlflow-env/bin/activate
```

```bash
export MLFLOW_TRACKING_URI=http://127.0.0.1:5000
```

```bash
mlflow models serve \
  -m "models:/iris-model@production" \
  -p 6000 \
  --no-conda
```

## View registered models

Go to:

- Model training → Models

![MLflow Model Registry showing model versions#center](images/mlflow-model.png "MLflow Model Registry with versions")

## Test inference from the Free Terminal ( A or C)

```bash
curl -X POST http://127.0.0.1:6000/invocations \
  -H "Content-Type: application/json" \
  -d '{
    "dataframe_records": [
      {
        "sepal length (cm)": 5.1,
        "sepal width (cm)": 3.5,
        "petal length (cm)": 1.4,
        "petal width (cm)": 0.2
      }
    ]
```

## What you've learned

You have successfully:

- Versioned models
- Selected the best model
- Assigned production alias
- Served model as API
- Performed inference
