---
title: Install XGBoost and train machine learning models
weight: 7

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install and configure XGBoost on SUSE Linux

In this section, you'll install XGBoost on a GCP Arm64 (Axion) virtual machine (VM) running SUSE Linux with Python 3.11. You'll then train machine learning models and tune model performance using hyperparameter optimization.

You'll use:

**Terminal A** → Setup and training

**Terminal B** → Benchmarking and tuning

Open both terminals connected to the VM before starting.

## Connect to the VM

Connect to the VM using terminal A:

```bash
ssh <your-user>@<your-vm-ip>
```

## Update the system

```bash
sudo zypper refresh
sudo zypper update -y
```

## Install required dependencies

```bash
sudo zypper install -y \
  python311 \
  python311-pip \
  python311-devel \
  gcc \
  gcc-c++ \
  make \
  git \
  wget
```

**Verify Python:**

```bash
python3.11 --version
```

## Create Python environment

```bash
mkdir -p ~/xgboost-learning-path
cd ~/xgboost-learning-path

python3.11 -m venv xgb-env
source xgb-env/bin/activate
```

## Upgrade pip tools:

```bash
pip install --upgrade pip setuptools wheel
```

## Create requirements file

```bash
cat > requirements.txt <<'EOF'
xgboost
numpy
pandas
scikit-learn
matplotlib
joblib
EOF
```

## Install dependencies:

```bash
pip install -r requirements.txt
```

**Verify XGBoost:**

```bash
python -c "import xgboost; print(xgboost.__version__)"
```

The output is similar to:

```output
3.2.0
```

## Create XGBoost training script

```bash
cat > train_xgboost.py <<'EOF'
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
import xgboost as xgb
import joblib
import time

data = load_breast_cancer()

X_train, X_test, y_train, y_test = train_test_split(
    data.data,
    data.target,
    test_size=0.2,
    random_state=42
)

model = xgb.XGBClassifier(
    n_estimators=200,
    max_depth=6,
    learning_rate=0.1,
    tree_method="hist",
    eval_metric="logloss"
)

start = time.time()

model.fit(X_train, y_train)

end = time.time()

predictions = model.predict(X_test)

accuracy = accuracy_score(y_test, predictions)

print(f"Model Accuracy : {accuracy:.4f}")
print(f"Training Time  : {end - start:.2f} seconds")

model.save_model("xgboost-model.json")

joblib.dump(model, "xgboost-model.pkl")

print("Model saved successfully")
EOF
```

## Train the model

```bash
python train_xgboost.py
```

The output is similar to:

```output
Model Accuracy : 0.9561
Training Time  : 0.04 seconds
Model saved successfully
```

## Verify generated model files

```bash
ls -lh
```

The output is similar to:

```output
requirements.txt
train_xgboost.py
xgb-env
xgboost-model.json
xgboost-model.pkl
```

## Hyperparameter tuning

Create the tuning script:

```bash
cat > tune_xgboost.py <<'EOF'
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split, GridSearchCV
import xgboost as xgb

data = load_breast_cancer()

X_train, X_test, y_train, y_test = train_test_split(
    data.data,
    data.target,
    test_size=0.2,
    random_state=42
)

params = {
    "max_depth": [4, 6, 8],
    "learning_rate": [0.01, 0.1],
    "n_estimators": [100, 200]
}

model = xgb.XGBClassifier(
    tree_method="hist",
    eval_metric="logloss"
)

grid = GridSearchCV(
    model,
    params,
    cv=3,
    n_jobs=-1
)

grid.fit(X_train, y_train)

print("\nBest Parameters:")
print(grid.best_params_)
EOF
```

**Run:**

```bash
python tune_xgboost.py
```

The output is similar to:

```output
Best Parameters:
{'learning_rate': 0.1, 'max_depth': 4, 'n_estimators': 100}
```

## Benchmark large-scale training

Create benchmark script:

```bash
cat > benchmark_xgboost.py <<'EOF'
from sklearn.datasets import make_classification
import xgboost as xgb
import time

X, y = make_classification(
    n_samples=500000,
    n_features=50,
    n_informative=25,
    random_state=42
)

model = xgb.XGBClassifier(
    n_estimators=300,
    max_depth=8,
    tree_method="hist",
    eval_metric="logloss"
)

start = time.time()

model.fit(X, y)

end = time.time()

print(f"\nBenchmark completed in {end - start:.2f} seconds")
EOF
```

## Run benchmark

```bash
python benchmark_xgboost.py
```

The output is similar to:

```output
Benchmark completed in 9.36 seconds
```

## What you've accomplished and what's next

You've successfully:

* Installed XGBoost on GCP Axion Arm64
* Configured Python 3.11 environment
* Trained XGBoost models on Arm processors
* Tuned hyperparameters
* Benchmarked large-scale datasets

Next, you'll deploy the trained model as an inference API and access it from your browser using the VM public IP.
