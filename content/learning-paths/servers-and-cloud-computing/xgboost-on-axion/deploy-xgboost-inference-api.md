---
title: Deploy and access XGBoost inference API
weight: 8

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Deploy XGBoost inference API on SUSE Linux

In this section, you'll deploy the trained XGBoost model as a Flask-based inference API on a GCP Axion Arm64 VM. You'll expose the API externally and access it from a browser using the VM public IP.

You'll use:

**Terminal A** → API server

**Terminal B** → API testing

## Connect to the VM
Connect to the VM:

```bash
ssh <your-user>@<your-vm-ip>
```

Navigate to the project:

```bash
cd ~/xgboost-learning-path
```

Activate environment:

```bash
source xgb-env/bin/activate
```

## Install Flask

Create updated requirements:

```bash
cat > requirements.txt <<'EOF'
xgboost
numpy
pandas
scikit-learn
matplotlib
joblib
flask
EOF
```

**Install Flask:**

```bash
pip install -r requirements.txt
```

**Verify:**

```bash
pip list | grep Flask
```

The output is similar to:

```output
Flask            3.1.3
```

## Create inference API

```bash
cat > inference_api.py <<'EOF'
from flask import Flask, request, jsonify
import numpy as np
import joblib

app = Flask(__name__)

model = joblib.load("xgboost-model.pkl")

@app.route("/", methods=["GET"])
def home():
    return """
    <h1>XGBoost API Running on GCP Axion Arm64</h1>
    <p>Inference API Status : Active</p>
    <p>Use POST /predict endpoint for predictions.</p>
    """

@app.route("/predict", methods=["POST"])
def predict():

    try:
        data = request.json["features"]

        prediction = model.predict(np.array([data]))

        return jsonify({
            "prediction": int(prediction[0])
        })

    except Exception as e:
        return jsonify({
            "error": str(e)
        })

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8080)
EOF
```

## Start the inference API

Run:

```bash
python inference_api.py
```

The output is similar to:

```output
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:8080
 * Running on http://10.128.15.209:8080
```

Leave this terminal running.

## Access API from browser

Open:

```text
http://<VM-PUBLIC-IP>:8080
``~

Example:

```text
http://35.xxx.xxx.xxx:8080
```

The output is similar to:

```output
XGBoost Inference API is Running
API Status: Active

This API is running on Google Cloud Axion Arm64.

Use the POST /predict endpoint to send prediction requests.
```

## Test inference locally

Open terminal B:

```bash
cd ~/xgboost-learning-path
source xgb-env/bin/activate
```

Run:

```bash
curl -X POST http://127.0.0.1:8080/predict \
-H "Content-Type: application/json" \
-d '{"features":[17.99,10.38,122.8,1001.0,0.1184,0.2776,0.3001,0.1471,0.2419,0.07871,1.095,0.9053,8.589,153.4,0.006399,0.04904,0.05373,0.01587,0.03003,0.006193,25.38,17.33,184.6,2019.0,0.1622,0.6656,0.7119,0.2654,0.4601,0.1189]}'
```

The output is similar to:

```output
{"prediction":0}
```

## What you've accomplished

You've successfully:

* Deployed XGBoost inference API on GCP Axion Arm64
* Exposed the API externally
* Accessed the API using the VM public IP
* Performed inference using REST API requests
