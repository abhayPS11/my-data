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

    To Install:    pts/tensorflow-lite-1.1.0

    Determining File Requirements ..............................................................................................
    Searching Download Caches ..................................................................................................

    1 Test To Install
        7 Files To Download [691MB]
        1500MB Of Disk Space Is Needed
        8 Seconds Estimated Install Time

    pts/tensorflow-lite-1.1.0:
        Test Installation 1 of 1
        7 Files Needed [691 MB]
        Downloading: tf-lite-20220518.tar.xz                                                                            [2.32MB]
        Downloading ............................................................................................................
        Downloading: mobilenet_v1_1.0_224.tgz                                                                          [89.95MB]
        Downloading ............................................................................................................
        Downloading: mobilenet_v1_1.0_224_quant.tgz                                                                    [33.45MB]
        Estimated Download Time: 1m ............................................................................................
        Downloading: nasnet_mobile_2018_04_27.tgz                                                                      [37.73MB]
        Estimated Download Time: 1m ............................................................................................
        Downloading: squeezenet_2018_04_27.tgz                                                                          [8.87MB]
        Estimated Download Time: 1m ............................................................................................
        Downloading: inception_resnet_v2_2018_04_27.tgz                                                                  [216MB]
        Estimated Download Time: 1m ............................................................................................
        Downloading: inception_v4_2018_04_27.tgz                                                                         [303MB]
        Estimated Download Time: 1m ............................................................................................
        Approximate Install Size: 1500 MB
        Estimated Install Time: 8 Seconds
        Installing Test @ 05:29:24



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
    Estimated Test Run-Time:      6 Minutes
    Estimated Time To Completion: 32 Minutes [06:01 UTC]
        Started Run 1 @ 05:29:42
        Started Run 2 @ 05:30:47
        Started Run 3 @ 05:31:51

    Model: SqueezeNet:
        7438.62
        7436.32
        7452.87

    Average: 7442.60 Microseconds
    Deviation: 0.12%

    Comparison of 681 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 3956 Microseconds. Box plot of samples:
    [                                                                                 |-------*-----------------*-----------*-*#*!*]
                                                                                            This Result (20th Percentile): 7443 ^
                                                                      ARMv8 Cortex-A72: 76284 ^    2 x Intel Xeon Gold 6342: 3175 ^
                                                                                                  2 x AMD EPYC 7773X: 15378 ^
                                                                                                     AMD Ryzen 5 3400G: 11679 ^
                                                                                       AMD Ryzen 3 3200U: 39381 ^

TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0 [Model: Inception V4]
    Test 2 of 6
    Estimated Trial Run Count:    3
    Estimated Test Run-Time:      4 Minutes
    Estimated Time To Completion: 16 Minutes [05:48 UTC]
        Started Run 1 @ 05:33:03
        Started Run 2 @ 05:34:07
        Started Run 3 @ 05:35:12

    Model: Inception V4:
        99053.6
        99952
        99338

    Average: 99447.9 Microseconds
    Deviation: 0.46%

    Comparison of 661 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 45616 Microseconds. Box plot of samples:
    [                                                               *       |-------------------------------------------*--*-*#*#!*]
                                                                                          This Result (17th Percentile): 99448 ^
                                          ARMv8 Cortex-A72: 1222930 ^                            2 x Intel Xeon Gold 5220R: 30260 ^
                                                                                               Intel Xeon E3-1231 v3: 127308 ^
                                                                                                Intel Core i3-7100: 171470 ^
                                                                                              ARMv8 Cortex-A78E: 222606 ^

TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0 [Model: NASNet Mobile]
    Test 3 of 6
    Estimated Trial Run Count:    3
    Estimated Test Run-Time:      4 Minutes
    Estimated Time To Completion: 13 Minutes [05:48 UTC]
        Started Run 1 @ 05:36:24
        Started Run 2 @ 05:37:28
        Started Run 3 @ 05:38:33

    Model: NASNet Mobile:
        13640.6
        13644.6
        13639.2

    Average: 13641.5 Microseconds
    Deviation: 0.02%

    Comparison of 531 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 16526 Microseconds. Box plot of samples:
    [                                                                    |-------------------------------------------------------#*]
                                                                                                             AMD EPYC 7551: 54374 ^

TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0 [Model: Mobilenet Float]
    Test 4 of 6
    Estimated Trial Run Count:    3
    Estimated Test Run-Time:      4 Minutes
    Estimated Time To Completion: 10 Minutes [05:48 UTC]
        Started Run 1 @ 05:39:44
        Started Run 2 @ 05:40:48
        Started Run 3 @ 05:41:53

    Model: Mobilenet Float:
        5130.54
        5121.6
        5118.99

    Average: 5123.71 Microseconds
    Deviation: 0.12%

    Comparison of 908 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 2884 Microseconds. Box plot of samples:
    [         *                |-----*------------------------------------------------------*---------------------------*--*#*!#*|*]
                                                                                       This Result (19th Percentile): 5124 ^
                                     ^ Intel Core i7-7700: 55387   AMD Ryzen 3 3200U: 23400 ^          Intel Xeon Gold 6342: 1297 ^
              ^ ARMv8 Cortex-A72: 68651                                                          2 x Intel Xeon Gold 6144: 2072 ^
                                                                                                 Intel Xeon E3-1275 v6: 4257 ^
                                                                                            Intel Xeon E5-2609 v4: 7159 ^

TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0 [Model: Mobilenet Quant]
    Test 5 of 6
    Estimated Trial Run Count:    3
    Estimated Test Run-Time:      4 Minutes
    Estimated Time To Completion: 7 Minutes [05:49 UTC]
        Started Run 1 @ 05:43:04
        Started Run 2 @ 05:44:09
        Started Run 3 @ 05:45:13

    Model: Mobilenet Quant:
        2220.98
        2235.72
        2218.49

    Average: 2225.06 Microseconds
    Deviation: 0.42%

    Comparison of 682 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 3938 Microseconds. Box plot of samples:
    [ |----------------------------------------------------------------------------------------------*-------------------------##*|]
                                                                                    AmpereOne: 57670 ^       AMD EPYC 7F32: 5163 ^

TensorFlow Lite 2022-05-18:
    pts/tensorflow-lite-1.1.0 [Model: Inception ResNet V2]
    Test 6 of 6
    Estimated Trial Run Count:    3
    Estimated Time To Completion: 4 Minutes [05:49 UTC]
        Started Run 1 @ 05:46:25
        Started Run 2 @ 05:47:29
        Started Run 3 @ 05:48:34

    Model: Inception ResNet V2:
        90144.9
        90686.4
        90503.1

    Average: 90444.8 Microseconds
    Deviation: 0.30%

    Comparison of 596 OpenBenchmarking.org samples since 19 May 2022 to 7 October; median result: 59528 Microseconds. Box plot of samples:
    [                                                                      |*----------------------------------*----------*--*#*!#*]
                                                                                          This Result (29th Percentile): 90445 ^
                                                  ARMv8 Cortex-A72: 1086763 ^                         Intel Xeon Gold 6346: 29545 ^
                                                                                               Intel Xeon E5-2609 v4: 145107 ^
                                                                                                ARMv8 Cortex-A78E: 203200 ^
                                                                                     AMD Ryzen 3 3200U: 407501 ^

    Percentile Classification Of Current Benchmark Run
    SYSTEM
        TensorFlow Lite
            SqueezeNet:       20th
            Inception V4:     17th
            NASNet Mobile:    67th
            Mobilenet Float:  19th
            Mobilenet Quant:  80th
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

| **Model**             | **Average Inference Time (µs)** | **Deviation (%)** | **Percentile (vs OpenBenchmarking.org)** | **Median Reference (µs)** |
|------------------------|----------------------------------|--------------------|------------------------------------------|----------------------------|
| **SqueezeNet**         | 7187.09                         | 0.27               | 21st                                     | 3956                       | 
| **Inception V4**       | 108500                          | 0.12               | 16th                                     | 45616                      | 
| **NASNet Mobile**      | 17111.4                         | 4.01               | 47th                                     | 16526                      |
| **Mobilenet Float**    | 4103.64                         | 0.78               | 28th                                     | 2884                       | 
| **Mobilenet Quant**    | 12837.5                         | 1.52               | 11th                                     | 3938                       | 
| **Inception ResNet V2**| 92700.1                         | 1.36               | 28th                                     | 59528                      | 

### Benchmark summary on Arm64
Results from the earlier run on the `c4a-standard-4` (4 vCPU, 16 GB memory) Arm64 VM in GCP (SUSE):

| **Model**             | **Average Inference Time (µs)** | **Deviation (%)** | **Percentile (vs OpenBenchmarking.org)** | **Median Reference (µs)** |
|-------------------------|-------------------------|------------|----------------------------------|-----------------------|
| **SqueezeNet**          | 7,442.60               | 0.12     | 20th                            | 3,956                 | 
| **Inception V4**        | 99,447.9               | 0.46      | 17th                            | 45,616                |
| **NASNet Mobile**       | 13,641.5               | 0.02      | 67th                            | 16,526                | 
| **Mobilenet Float**     | 5,123.71               | 0.12      | 19th                            | 2,884                 |
| **Mobilenet Quant**     | 2,225.06               | 0.42      | 80th                            | 3,938                 | 
| **Inception ResNet V2** | 90,444.8               | 0.30      | 29th                            | 59,528                | 

### TensorFlow benchmarking comparison on Arm64 and x86_64

- **Arm platform shows strong efficiency in lightweight models**, with **NASNet Mobile** and **Mobilenet Quant** achieving **67th and 80th percentile** performance globally.  
- **Mobilenet Quant** achieved the **fastest inference** at **2,225 µs**, highlighting Arm’s advantage in quantized model workloads.  
- **Heavier models** like **Inception V4** and **Inception ResNet V2** show moderate performance due to higher compute intensity.  
- Overall, **Arm demonstrates competitive inference performance**, especially in **mobile and edge-optimized TensorFlow models**.
