---
title: Train and Benchmark AI Workloads on GCP Axion (Arm)
weight: 2

### FIXED, DO NOT MODIFY
layout: learningpathall
---

# Train and Benchmark AI Workloads on GCP Axion (Arm)

This section demonstrates neural network training and benchmarking on GCP Axion Arm64 processors using PyTorch.

## Learning Objectives

- Create AI training workloads
- Train neural network models
- Benchmark CPU workloads
- Measure Arm64 AI performance
- Validate large model execution


## Activate environment

```bash
source ~/deepspeed-env/bin/activate
```

Go to project directory:

```bash
cd ~/deepspeed-demo
```

## Create baseline training script

```bash
cat > train.py << 'EOF'
import torch
import torch.nn as nn
import torch.optim as optim
import time

class SimpleModel(nn.Module):
    def __init__(self):
        super().__init__()

        self.net = nn.Sequential(
            nn.Linear(128, 256),
            nn.ReLU(),
            nn.Linear(256, 64),
            nn.ReLU(),
            nn.Linear(64, 1)
        )

    def forward(self, x):
        return self.net(x)

model = SimpleModel()

optimizer = optim.Adam(model.parameters(), lr=0.001)

data = torch.randn(5000, 128)
target = torch.randn(5000, 1)

start = time.time()

for epoch in range(5):

    total_loss = 0

    for i in range(0, len(data), 32):

        x = data[i:i+32]
        y = target[i:i+32]

        output = model(x)

        loss = ((output - y) ** 2).mean()

        optimizer.zero_grad()

        loss.backward()

        optimizer.step()

        total_loss += loss.item()

    print(f"Epoch {epoch+1}, Loss: {total_loss}")

end = time.time()

print("Total Training Time:", end - start)
EOF
```


## Execute baseline training

```bash
python train.py
```

Expected output:

```text
Epoch 1, Loss: ...
Epoch 2, Loss: ...
Epoch 3, Loss: ...
Epoch 4, Loss: ...
Epoch 5, Loss: ...

Total Training Time: ...
```


## Benchmark baseline workload

```bash
time python train.py | tee pytorch_baseline_result.txt
```

Example output:

```text
Total Training Time: 0.8 seconds

real    0m2.xxs
```


## Create large benchmark workload

```bash
cat > train_large.py << 'EOF'
import torch
import torch.nn as nn
import torch.optim as optim
import time
import os

torch.set_num_threads(os.cpu_count())

class LargeModel(nn.Module):
    def __init__(self):
        super().__init__()

        self.net = nn.Sequential(
            nn.Linear(512, 1024),
            nn.ReLU(),
            nn.Linear(1024, 512),
            nn.ReLU(),
            nn.Linear(512, 128),
            nn.ReLU(),
            nn.Linear(128, 1)
        )

    def forward(self, x):
        return self.net(x)

model = LargeModel()

optimizer = optim.Adam(model.parameters(), lr=0.001)

data = torch.randn(20000, 512)
target = torch.randn(20000, 1)

start = time.time()

for epoch in range(5):

    total_loss = 0

    for i in range(0, len(data), 64):

        x = data[i:i+64]
        y = target[i:i+64]

        output = model(x)

        loss = ((output - y) ** 2).mean()

        optimizer.zero_grad()

        loss.backward()

        optimizer.step()

        total_loss += loss.item()

    print(f"Epoch {epoch+1}, Loss: {total_loss}")

end = time.time()

print("Total Training Time:", end - start)
EOF
```


## Run large benchmark

```bash
time python train_large.py | tee pytorch_large_result.txt
```

Expected output:

```text
Epoch 1, Loss: ...
Epoch 2, Loss: ...
Epoch 3, Loss: ...
Epoch 4, Loss: ...
Epoch 5, Loss: ...

Total Training Time: ...
```


## Monitor CPU utilization

Open another terminal.

Run:

```bash
top
```

In the first terminal:

```bash
python train_large.py
```

Observe:

- CPU usage
- Memory utilization
- Python process behavior


## Save environment details

```bash
python --version | tee environment.txt

pip list | tee -a environment.txt

gcc --version | tee -a environment.txt

lscpu | tee -a environment.txt

free -h | tee -a environment.txt
```


## Verify generated files

```bash
ls -lh
```

Expected files:

```text
environment.txt
pytorch_baseline_result.txt
pytorch_large_result.txt
train.py
train_large.py
```


{{% notice Note %}}
DeepSpeed distributed execution was not used because:

- Default SUSE Arm64 images use GCC 7.x
- DeepSpeed native CPU communication extensions require GCC 9+
- DeepSpeed launcher attempts to compile `deepspeed_shm_comm`

For this reason, PyTorch CPU execution was used for workload validation and benchmarking.
{{% /notice %}}

## Benchmark observations

| Workload | Approx Training Time |
|---|---|
| Baseline Model | ~0.8 seconds |
| Large Model | ~5.4 seconds |


## What you've learned

- Created AI training workloads
- Trained neural network models
- Benchmarked Arm64 AI workloads
- Validated CPU-based AI execution
- Measured large workload performance
 Run ONNX Runtime benchmarks
- Optimize AI workloads for Arm64
- Explore distributed AI training
