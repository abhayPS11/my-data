---
title: Te Baseline Testing on Google Axion C4A Arm Virtual Machine
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## TensorFlow Baseline Testing on GCP SUSE VMs

### Verify Installation
Run a Python command to ensure TensorFlow can be imported:

```console
python -c "import tensorflow as tf; print(tf.__version__)"
```
### List Available Devices
Check which devices TensorFlow can access (CPU, GPU if any):

```console
python -c "import tensorflow as tf; print(tf.config.list_physical_devices())"
```

You should see an output similar to:
```output
[PhysicalDevice(name='/physical_device:CPU:0', device_type='CPU')]
```

### Run a Simple Computation
Perform a quick matrix multiplication to verify CPU computation:

```python
python -c "import tensorflow as tf; import time; 
a = tf.random.uniform((1000,1000)); b = tf.random.uniform((1000,1000));
start = time.time(); c = tf.matmul(a,b); end = time.time(); 
print('Computation time:', end - start, 'seconds')"
```
- This checks **CPU speed** and the correctness of basic operations.
- Note the **computation time** as your baseline.

You should see an output similar to:
```output
Computation time: 0.008263111114501953 seconds
```
### Test Neural Network Execution
Run a small neural network on dummy data:

Instead of running everything inline in one python -c command, save it as test_nn.py for clarity:

```console
vi test_nn.py
```
Paste this code into the file:

```python
import tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense
import numpy as np

# Dummy data
x = np.random.rand(1000, 20)
y = np.random.rand(1000, 1)

# Define the model
model = Sequential([
    Dense(64, activation='relu', input_shape=(20,)),
    Dense(1)
])

# Compile the model
model.compile(optimizer='adam', loss='mse')

# Train for 1 epoch
model.fit(x, y, epochs=1, batch_size=32)
```

**Run the Script**
Execute the script with Python:

```console
python test_nn.py
```

**Output**
TensorFlow will print training progress, like:
```output
32/32 ━━━━━━━━━━━━━━━━━━━━ 0s 1ms/step - loss: 0.1024
```

This confirms that TensorFlow are working properly on your Arm64 VM.
