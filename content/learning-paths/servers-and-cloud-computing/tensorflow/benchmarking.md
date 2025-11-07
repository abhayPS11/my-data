---
title: TensorFlow Benchmarking
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


##  TensorFlow Benchmark on GCP SUSE Arm64 VM
This guide demonstrates how to **benchmark TensorFlow Lite performance** on a Google Cloud **SUSE Linux ARM64 (C4A or Axion)** virtual machine using the `benchmark_model` binary and a converted MobileNetV2 model.

### Download TensorFlow Lite Benchmark Tool

```console
wget -O benchmark_model https://storage.googleapis.com/tensorflow-nightly-public/prod/tensorflow/release/lite/tools/nightly/latest/linux_aarch64_benchmark_model
chmod +x benchmark_model
```
This binary measures performance metrics (latency, throughput, etc.) for TensorFlow Lite models.

### Create the Model Conversion Script
Create a Python script to convert a pre-trained MobileNetV2 Keras model to TensorFlow Lite format.

```console
vi convert_mobilenet.py
```

Paste the following content:

```python
import tensorflow as tf

# Load the MobileNetV2 Keras model
print("Loading Keras MobileNetV2...")
model = tf.keras.applications.MobileNetV2(weights="imagenet")

# Convert the model to TensorFlow Lite format
print("Converting Keras model to TFLite...")
converter = tf.lite.TFLiteConverter.from_keras_model(model)
tflite_model = converter.convert()

# Save the converted model
tflite_model_path = "mobilenet_v2_224.tflite"
with open(tflite_model_path, "wb") as f:
    f.write(tflite_model)

print(f"TFLite model saved as: {tflite_model_path}")
```

### Run the Model Conversion

```console
python3 convert_mobilenet.py
```

Once complete, the `mobilenet_v2_224.tflite` file will be generated in the current directory.

### Benchmark Execution
Run the benchmark using the TensorFlow Lite benchmarking tool:

```console
./benchmark_model \
  --graph=mobilenet_v2_224.tflite \
  --num_threads=4 \
  --warmup_runs=5 \
  --num_runs=50 \
  --verbose=false
```

- **--graph=mobilenet_v2_224.tflite** → Specifies the TensorFlow Lite model file to benchmark.
- **--num_threads=4** → Sets the number of CPU threads used for inference.
- **--warmup_runs=5** → Runs a few untimed inferences to stabilize performance before measuring.
- **--num_runs=50**→ Defines how many timed inference runs to execute for averaging results.
- **--verbose=false** → Disables detailed per-run logs, showing only summary results.

You should see an output similar to:
```output
INFO: Min num runs: [50]
INFO: Num threads: [4]
INFO: Min warmup runs: [5]
INFO: Graph: [mobilenet_v2_224.tflite]
INFO: Signature to run: []
INFO: #threads used for CPU inference: [4]
INFO: Loaded model mobilenet_v2_224.tflite
INFO: Created TensorFlow Lite XNNPACK delegate for CPU.
INFO: The input model file size (MB): 13.9193
INFO: Initialized session in 6.395ms.
INFO: Running benchmark for at least 5 iterations and at least 0.5 seconds but terminate if exceeding 150 seconds.
INFO: count=153 first=2761 curr=2885 min=2605 max=18080 avg=3243.58 std=2165 p5=2630 median=2761 p95=5331

INFO: Running benchmark for at least 50 iterations and at least 1 seconds but terminate if exceeding 150 seconds.
INFO: count=337 first=2881 curr=2703 min=2605 max=20356 avg=2942.27 std=1190 p5=2647 median=2781 p95=3169

INFO: Inference timings in us: Init: 6395, First inference: 2761, Warmup (avg): 3243.58, Inference (avg): 2942.27
INFO: Note: as the benchmark tool itself affects memory footprint, the following is only APPROXIMATE to the actual memory footprint of the model at runtime. Take the information at your discretion.
INFO: Memory footprint delta from the start of the tool (MB): init=39.4531 overall=40.8281
```
### Benchmark Metrics Explanation

- **Model**: The name of the TensorFlow Lite model being benchmarked — in this case, mobilenet_v2_224.tflite, a lightweight image classification model.
- **Model File Size (MB)**:	The size of the .tflite model file on disk. Smaller models load faster and use less memory.
- **CPU Delegate**:	The backend used for running the inference. XNNPACK is an optimized delegate for ARM CPUs that accelerates performance.
- **Threads Used**:	Number of CPU threads used for inference. More threads can increase parallelism but may increase CPU load.
- **Warmup Runs**:	Number of initial runs to stabilize performance before actual benchmarking — avoids measuring cold-start effects.
- **Benchmark Runs**:	The total number of inference iterations performed during measurement for statistical accuracy.
- **Initialization Time (ms)**:	Time taken to initialize the TensorFlow Lite interpreter and load the model into memory.
- **Inference Avg Time (µs)**	Average time per inference across all benchmark runs — key measure of model speed and efficiency.
- **Median Inference Time (µs)**:	Middle value of all inference times — less affected by outliers, represents typical latency.
- **95th Percentile (p95) (µs)**	Inference time below which 95% of runs complete — indicates performance consistency.
- **Standard Deviation (µs)**	Variability in inference time — smaller values show more stable performance.
- **Memory Footprint (MB)**:	Approximate RAM usage during model inference, including model load and intermediate tensors.

### Benchmark summary on x86_64
To compare the benchmark results, the following results were collected by running the same benchmark on a `x86 - c4-standard-4` (4 vCPUs, 15 GB Memory) x86_64 VM in GCP, running SUSE:

### Benchmark summary on Arm64
Results from the earlier run on the `c4a-standard-4` (4 vCPU, 16 GB memory) Arm64 VM in GCP (SUSE):

| **Metric**                   | **Value**     | **Metric**                     | **Value**     |
|------------------------------|----------------|----------------------------------|----------------|
| Model Name                   | mobilenet_v2_224.tflite | Model Size (MB)              | 13.9193        |
| Threads Used                 | 4              | Warmup Runs                    | 5              |
| Benchmark Runs               | 50             | Initialization Time (µs)       | 6395           |
| First Inference Time (µs)    | 2761           | Warmup Avg Time (µs)           | 3243.58        |
| Avg Inference Time (µs)      | 2942.27        | Min Inference Time (µs)        | 2605           |
| Max Inference Time (µs)      | 20356          | Median Inference Time (µs)     | 2781           |
| p95 Inference Time (µs)      | 3169           | Std Deviation (µs)             | 1190           |
| Init Memory (MB)             | 39.45          | Overall Memory (MB)            | 40.83          |

### TensorFlow benchmarking comparison on Arm64 and x86_64

