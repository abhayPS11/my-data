---
title: Deploy MLflow on GCP SUSE Arm64 (Setup & Tracking)
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Deploy MLflow on GCP SUSE Arm64

This section guides you through setting up MLflow on a GCP Arm64 (Axion) virtual machine using SUSE Linux and Python 3.11.

You will install dependencies, configure the MLflow tracking server, and run experiments.

## Terminal usage

This guide uses **two terminals only**:

- **Terminal A** → Setup, training, and scripts  
- **Terminal B** → Runs MLflow server (keep it running)


## Connect to the VM

Open **Terminal A** and connect:

```bash
ssh <your-user>@<your-vm-ip>
```

## Verify system:

```bash
uname -m
cat /etc/os-release
```

The output is similar to:

```output
aarch64
NAME="SLES"
VERSION="15-SP5"
VERSION_ID="15.5"
PRETTY_NAME="SUSE Linux Enterprise Server 15 SP5"
ID="sles"
ID_LIKE="suse"
ANSI_COLOR="0;32"
CPE_NAME="cpe:/o:suse:sles:15:sp5"
DOCUMENTATION_URL="https://documentation.suse.com/"
```

## Update your system

```bash
sudo zypper refresh
sudo zypper update -y
```

## Install required dependencies

```bash
sudo zypper install -y \
  python311 \
  python311-pip \
  python311-setuptools \
  python311-wheel \
  sqlite3 \
  gcc \
  gcc-c++ \
  make \
  git
```

**Verify:**

```bash
python3.11 --version
pip3.11 --version
```

The output is similar to:

```output
Python 3.11.10
pip 26.0.1 from /home/gcpuser/mlflow-learning-path/mlflow-env/lib64/python3.11/site-packages/pip (python 3.11)
```

## Create Python environment

```bash
mkdir -p ~/mlflow-learning-path
cd ~/mlflow-learning-path

python3.11 -m venv mlflow-env
source mlflow-env/bin/activate
```

**Upgrade tools:**

```bash
pip install --upgrade pip setuptools wheel
```

## Install MLflow

```bash
pip install mlflow scikit-learn pandas numpy matplotlib
```

## Create directories

```bash
mkdir -p backend artifacts demo
touch backend/mlflow.db
```

## Start MLflow server

**Now open Terminal B and run:**

```bash
ssh <your-user>@<your-vm-ip>
cd ~/mlflow-learning-path
source mlflow-env/bin/activate
``

```bahs
mlflow server \
  --host 0.0.0.0 \
  --port 5000 \
  --backend-store-uri sqlite:///$(pwd)/backend/mlflow.db \
  --artifacts-destination file://$(pwd)/artifacts \
  --allowed-hosts "*" \
  --cors-allowed-origins "*"
```

## Access MLflow UI

**Open in browser:**

```text
http://<VM-IP>:5000
```

- Select Model training

## Create training script

**Back in Terminal A:**

```bash
cd ~/mlflow-learning-path/demo
```

```bash
cat > train.py <<'EOF'
import os
import mlflow
import mlflow.sklearn
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score

mlflow.set_tracking_uri("http://127.0.0.1:5000")
mlflow.set_experiment("iris-exp")

X, y = load_iris(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(X, y)

C = float(os.getenv("C", 1.0))

with mlflow.start_run():
    model = LogisticRegression(C=C, max_iter=200)
    model.fit(X_train, y_train)

    preds = model.predict(X_test)
    acc = accuracy_score(y_test, preds)

    mlflow.log_param("C", C)
    mlflow.log_metric("accuracy", acc)

    mlflow.sklearn.log_model(
        model,
        name="model",
        registered_model_name="iris-model"
    )

    print("Accuracy:", acc)
EOF
```

## Run experiments

```bash
export MLFLOW_TRACKING_URI=http://127.0.0.1:5000
```

```bash
python train.py
export C=0.5
python train.py
export C=2.0
python train.py
```

The output is similar to:

```output
Registered model 'iris-model' already exists. Creating a new version of this model...
2026/04/22 04:44:42 INFO mlflow.store.model_registry.abstract_store: Waiting up to 300 seconds for model version to finish creation. Model name: iris-model, version 2
Created version '2' of model 'iris-model'.
Accuracy: 1.0
🏃 View run unequaled-grub-345 at: http://127.0.0.1:5000/#/experiments/1/runs/8a9e3cf1ab3b4f669a37fb0cbe1e469c
🧪 View experiment at: http://127.0.0.1:5000/#/experiments/1

(mlflow-env) gcpuser@mlfow-new:~/mlflow-learning-path/demo> export C=0.5
(mlflow-env) gcpuser@mlfow-new:~/mlflow-learning-path/demo> python train.py

Registered model 'iris-model' already exists. Creating a new version of this model...
2026/04/22 04:44:52 INFO mlflow.store.model_registry.abstract_store: Waiting up to 300 seconds for model version to finish creation. Model name: iris-model, version 3
Created version '3' of model 'iris-model'.
Accuracy: 1.0
🏃 View run vaunted-horse-22 at: http://127.0.0.1:5000/#/experiments/1/runs/4e10db3266c5495bae43df36a46ebd13
🧪 View experiment at: http://127.0.0.1:5000/#/experiments/1

(mlflow-env) gcpuser@mlfow-new:~/mlflow-learning-path/demo> export C=2.0
(mlflow-env) gcpuser@mlfow-new:~/mlflow-learning-path/demo> python train.py

2026/04/22 04:45:00 INFO mlflow.store.model_registry.abstract_store: Waiting up to 300 seconds for model version to finish creation. Model name: iris-model, version 4
Created version '4' of model 'iris-model'.
Accuracy: 1.0
🏃 View run defiant-hound-237 at: http://127.0.0.1:5000/#/experiments/1/runs/8b2517b64bb34df199a78f2a8b29137c
🧪 View experiment at: http://127.0.0.1:5000/#/experiments/1
```

## Verify results

In UI:

- Open Model training
- Select iris-exp
- Go to Runs

**You should see:**

- Multiple runs
- Accuracy metrics
- Model versions (v1, v2, v3)


## What you've learned

You have successfully:

- Installed MLflow on SUSE ARM64
- Configured Python 3.11 environment
- Started MLflow tracking server
- Logged experiments and metrics
- Registered models

Next, you will deploy and serve the model.
