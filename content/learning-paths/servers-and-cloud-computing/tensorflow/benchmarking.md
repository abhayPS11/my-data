---
title: TensorFlow Benchmarking
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## TensorFlow Benchmarking using Phoronix Test Suite (PTS)
This guide demonstrates how to benchmark TensorFlow performance on a **GCP SUSE Arm64 VM** using the **Phoronix Test Suite (PTS)**. It provides consistent, cross-platform performance metrics for fair comparison.

**Phoronix Test Suite (PTS)** is an open-source benchmarking tool that automates performance testing across different hardware and software platforms.
It is used for TensorFlow benchmarking to provide consistent and repeatable measurements of model performance, and to compare **x86** and **Arm64** architectures fairly.

### Install Dependencies
Run the following commands to refresh your system and install the essentials:

```console
sudo zypper refresh
sudo zypper install -y wget tar python3 python3-pip
```

### Install Phoronix Test Suite
Download and install Phoronix Test Suite, a tool that automates performance testing. 

```console
wget https://phoronix-test-suite.com/releases/phoronix-test-suite-10.8.4.tar.gz
tar -xvzf phoronix-test-suite-10.8.4.tar.gz
cd phoronix-test-suite
sudo ./install-sh
```

**Verify installation:**

Run the command below to confirm it’s installed correctly.

```console
phoronix-test-suite version
```

### List TensorFlow Benchmarks
Use the command below to list all available TensorFlow benchmark options.:

```console
phoronix-test-suite list-available-tests | grep tensorflow
```
You should see an output similar to:
```output
pts/intel-tensorflow        Intel TensorFlow                                  System
pts/tensorflow              TensorFlow                                        System
pts/tensorflow-lite         TensorFlow Lite                                   System
pts/zendnn-tensorflow       AMD ZenDNN TensorFlow                             System
```

### Run TensorFlow Benchmark
This measures how fast and efficient TensorFlow Lite runs on your system — giving you detailed performance results.

```console
phoronix-test-suite benchmark pts/tensorflow-lite
```
You should see an output similar to:
```output
Phoronix Test Suite v10.8.4

    Installed:     pts/tensorflow-lite-1.1.0


TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0
    System Test Configuration
        1: Mobilenet Float
        2: Mobilenet Quant
        3: NASNet Mobile
        4: SqueezeNet
        5: Inception ResNet V2
        6: Inception V4
        7: Test All Options
        ** Multiple items can be selected, delimit by a comma. **
        Model: 7


System Information


  PROCESSOR:              ARMv8
    Core Count:           4
    Cache Size:           80 MB

  GRAPHICS:

  MOTHERBOARD:            KVM Google Compute Engine
    Network:              Google Compute Engine Virtual

  MEMORY:                 16GB

  DISK:                   107GB nvme_card-pd
    File-System:          xfs
    Mount Options:        attr2 inode64 logbsize=32k logbufs=8 noquota relatime rw
    Disk Scheduler:       NONE
    Disk Details:         Block Size: 4096

  OPERATING SYSTEM:       SUSE 15.6
    Kernel:               6.4.0-150600.23.73-default (aarch64)
    Compiler:             GCC 7.5.0 + Clang 17.0.6 + LLVM 17.0.6
    System Layer:         google
    Security:             gather_data_sampling: Not affected
                          + indirect_target_selection: Not affected
                          + itlb_multihit: Not affected
                          + l1tf: Not affected
                          + mds: Not affected
                          + meltdown: Not affected
                          + mmio_stale_data: Not affected
                          + reg_file_data_sampling: Not affected
                          + retbleed: Not affected
                          + spec_rstack_overflow: Not affected
                          + spec_store_bypass: Mitigation of SSB disabled via prctl
                          + spectre_v1: Mitigation of __user pointer sanitization
                          + spectre_v2: Mitigation of CSV2 BHB
                          + srbds: Not affected
                          + tsa: Not affected
                          + tsx_async_abort: Not affected
                          + vmscape: Not affected

    Would you like to save these test results (Y/n): n

TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0 [Model: SqueezeNet]
    Test 1 of 6
    Estimated Trial Run Count:    3
    Estimated Test Run-Time:      4 Minutes
    Estimated Time To Completion: 19 Minutes [07:28 UTC]
        Started Run 1 @ 07:10:07
        Started Run 2 @ 07:11:12
        Started Run 3 @ 07:12:17

    Model: SqueezeNet:
        7366.47
        7336.11
        7369.77

    Average: 7357.45 Microseconds
    Deviation: 0.25%

    Comparison of 681 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 3956 Microseconds. Box plot of samples:
    [                                                                                 |-------*-----------------*-----------*-*#*!*]
                                                                                            This Result (21st Percentile): 7357 ^
                                                                      ARMv8 Cortex-A72: 76284 ^    2 x Intel Xeon Gold 6342: 3175 ^
                                                                                                  2 x AMD EPYC 7773X: 15378 ^
                                                                                                     AMD Ryzen 5 3400G: 11679 ^
                                                                                       AMD Ryzen 3 3200U: 39381 ^

TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0 [Model: Inception V4]
    Test 2 of 6
    Estimated Trial Run Count:    3
    Estimated Test Run-Time:      4 Minutes
    Estimated Time To Completion: 16 Minutes [07:28 UTC]
        Started Run 1 @ 07:13:28
        Started Run 2 @ 07:14:33
        Started Run 3 @ 07:15:38

    Model: Inception V4:
        98628.8
        98579.7
        98556.6

    Average: 98588.4 Microseconds
    Deviation: 0.04%

    Comparison of 661 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 45616 Microseconds. Box plot of samples:
    [                                                               *       |-------------------------------------------*--*-*#*#!*]
                                                                                          This Result (17th Percentile): 98588 ^
                                          ARMv8 Cortex-A72: 1222930 ^                            2 x Intel Xeon Gold 5220R: 30260 ^
                                                                                               Intel Xeon E3-1231 v3: 127308 ^
                                                                                                Intel Core i3-7100: 171470 ^
                                                                                              ARMv8 Cortex-A78E: 222606 ^

TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0 [Model: NASNet Mobile]
    Test 3 of 6
    Estimated Trial Run Count:    3
    Estimated Test Run-Time:      4 Minutes
    Estimated Time To Completion: 13 Minutes [07:28 UTC]
        Started Run 1 @ 07:16:49
        Started Run 2 @ 07:17:54
        Started Run 3 @ 07:18:58

    Model: NASNet Mobile:
        13387
        13321.4
        13479.9

    Average: 13396.1 Microseconds
    Deviation: 0.59%

    Comparison of 531 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 16526 Microseconds. Box plot of samples:
    [                                                                    |-------------------------------------------------------#*]
                                                                                                             AMD EPYC 7551: 54374 ^

TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0 [Model: Mobilenet Float]
    Test 4 of 6
    Estimated Trial Run Count:    3
    Estimated Test Run-Time:      4 Minutes
    Estimated Time To Completion: 10 Minutes [07:29 UTC]
        Started Run 1 @ 07:20:09
        Started Run 2 @ 07:21:14
        Started Run 3 @ 07:22:18

    Model: Mobilenet Float:
        5019.68
        5028.87
        5029.83

    Average: 5026.13 Microseconds
    Deviation: 0.11%

    Comparison of 908 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 2884 Microseconds. Box plot of samples:
    [         *                |-----*------------------------------------------------------*---------------------------*--*#*!#*|*]
                                                                                       This Result (19th Percentile): 5026 ^
                                     ^ Intel Core i7-7700: 55387   AMD Ryzen 3 3200U: 23400 ^          Intel Xeon Gold 6342: 1297 ^
              ^ ARMv8 Cortex-A72: 68651                                                          2 x Intel Xeon Gold 6144: 2072 ^
                                                                                                 Intel Xeon E3-1275 v6: 4257 ^
                                                                                            Intel Xeon E5-2609 v4: 7159 ^

TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0 [Model: Mobilenet Quant]
    Test 5 of 6
    Estimated Trial Run Count:    3
    Estimated Test Run-Time:      4 Minutes
    Estimated Time To Completion: 7 Minutes [07:29 UTC]
        Started Run 1 @ 07:23:30
        Started Run 2 @ 07:24:34
        Started Run 3 @ 07:25:39

    Model: Mobilenet Quant:
        2209.01
        2214.74
        2206.14

    Average: 2209.96 Microseconds
    Deviation: 0.20%

    Comparison of 682 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 3938 Microseconds. Box plot of samples:
    [ |----------------------------------------------------------------------------------------------*-------------------------##*|]
                                                                                    AmpereOne: 57670 ^       AMD EPYC 7F32: 5163 ^

TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0 [Model: Inception ResNet V2]
    Test 6 of 6
    Estimated Trial Run Count:    3
    Estimated Time To Completion: 4 Minutes [07:29 UTC]
        Started Run 1 @ 07:26:50
        Started Run 2 @ 07:27:55
        Started Run 3 @ 07:28:59

    Model: Inception ResNet V2:
        89863.4
        89946.7
        89852.9

    Average: 89887.7 Microseconds
    Deviation: 0.06%

    Comparison of 596 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 59528 Microseconds. Box plot of samples:
    [                                                                      |*----------------------------------*----------*--*#*!#*]
                                                                                          This Result (29th Percentile): 89888 ^
                                                  ARMv8 Cortex-A72: 1086763 ^                         Intel Xeon Gold 6346: 29545 ^
                                                                                               Intel Xeon E5-2609 v4: 145107 ^
                                                                                                ARMv8 Cortex-A78E: 203200 ^
                                                                                     AMD Ryzen 3 3200U: 407501 ^

    Percentile Classification Of Current Benchmark Run
    SYSTEM
        TensorFlow Lite
            SqueezeNet:       21st
            Inception V4:     17th
            NASNet Mobile:    68th
            Mobilenet Float:  19th
            Mobilenet Quant:  81st
            I.R.V:            29th
                              OpenBenchmarking.org Percentile

```

### Benchmark Metrics Explanation

- **Average Inference Time (µs):** The mean time, in microseconds, taken by the model to perform one inference across multiple runs.  
- **Deviation (%):** Represents the variation or stability of inference times; a lower value indicates more consistent performance.  
- **Percentile (vs OpenBenchmarking.org):** Indicates how your result ranks among all public submissions — higher percentiles reflect better performance.  
- **Median Reference (µs):** The median inference time from OpenBenchmarking.org is used as a baseline for comparison.  
- **Performance Comparison:** Describes how your system’s performance compares qualitatively to the global median result.  
- **Model Name:** Specifies the pre-trained TensorFlow Lite model evaluated (e.g., MobileNet, NASNet, Inception).

### Benchmark summary on x86_64
To compare the benchmark results, the following results were collected by running the same benchmark on a `x86 - c4-standard-4` (4 vCPUs, 15 GB Memory) x86_64 VM in GCP, running SUSE:

| **Model**               | **Average Inference Time (µs)** | **Deviation (%)** | **Percentile (vs OpenBenchmarking.org)** | **Median Reference (µs)** |
|--------------------------|-------------------------------:|------------------:|-----------------------------------------:|---------------------------:|
| **SqueezeNet**           | 7,204.03                       | 0.55              | 21st                                    | 3,956                     |
| **Inception V4**         | 112,070.0                      | 0.13              | 16th                                    | 45,616                    |
| **NASNet Mobile**        | 16,479.7                       | 0.60              | 50th                                    | 16,526                    |
| **Mobilenet Float**      | 4,201.81                       | 0.06              | 27th                                    | 2,884                     |
| **Mobilenet Quant**      | 13,081.1                       | 1.86              | 11th                                    | 3,938                     |
| **Inception ResNet V2**  | 91,500.8                       | 1.20              | 28th                                    | 59,528                    |

### Benchmark summary on Arm64
Results from the earlier run on the `c4a-standard-4` (4 vCPU, 16 GB memory) Arm64 VM in GCP (SUSE):

| **Model**               | **Average Inference Time (µs)** | **Deviation (%)** | **Percentile (vs OpenBenchmarking.org)** | **Median Reference (µs)** | 
|--------------------------|-------------------------------:|------------------:|-----------------------------------------:|---------------------------:|
| **SqueezeNet**           | 7,357.45                       | 0.25              | 21st                                    | 3,956                     |
| **Inception V4**         | 98,588.4                       | 0.04              | 17th                                    | 45,616                    | 
| **NASNet Mobile**        | 13,396.1                       | 0.59              | 68th                                    | 16,526                    |
| **Mobilenet Float**      | 5,026.13                       | 0.11              | 19th                                    | 2,884                     | 
| **Mobilenet Quant**      | 2,209.96                       | 0.20              | 81st                                    | 3,938                     | 
| **Inception ResNet V2**  | 89,887.7                       | 0.06              | 29th                                    | 59,528                    |

### TensorFlow benchmarking comparison on Arm64 and x86_64

- **Lightweight and quantized models** excelled on Arm64, with **Mobilenet Quant** achieving **81st percentile**, outperforming most global baselines in inference efficiency.  
- **NASNet Mobile** also performed exceptionally well (**68th percentile**), showcasing Arm’s strong optimization for mobile-oriented architectures.  
- **Mobilenet Float** and **SqueezeNet** maintained **solid throughput**, reflecting consistent CPU-level efficiency on Arm cores.  
- **Heavier deep CNNs** like **Inception V4** and **Inception ResNet V2** showed relatively higher inference times — typical for **compute-bound workloads** on CPU-only environments.  
- **Overall**, Arm64 delivers **competitive inference performance**, especially for lightweight and quantized TensorFlow models, making it ideal for **edge and on-device AI workloads**.
