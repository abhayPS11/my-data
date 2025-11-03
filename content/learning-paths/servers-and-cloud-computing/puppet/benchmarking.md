---
title: Puppet Benchmarking
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


##  Puppet Benchmark on GCP SUSE Arm64 VM

This guide explains how to perform a **Puppet standalone benchmark** on a **Google Cloud Platform (GCP) SUSE Linux Arm64 VM**.  
It measures Puppet’s local execution performance without requiring a Puppet Master.


### Prerequisites
Ensure Puppet is installed and working:

```console
puppet --version
```

### Create a Benchmark Manifest
Create a directory and a simple manifest file:

```console
mkdir -p ~/puppet-benchmark
cd ~/puppet-benchmark
vi benchmark.pp
```

Add the following content to the `benchmark.pp`:

```puppet
notify { 'Benchmark Test':
  message => 'Running Puppet standalone benchmark.',
}
```

### Run the Benchmark Command
Run Puppet in standalone mode using the `apply` command:

```console
time puppet apply benchmark.pp --verbose
```
This executes the manifest locally and outputs timing statistics.

You should see an output similar to:
```output
Notice: Compiled catalog for ########arm64.c.imperial-time-463411-q5.internal in environment production in 0.01 seconds
Info: Using environment 'production'
Info: Applying configuration version '1762160712'
Notice: Running Puppet standalone benchmark.
Notice: /Stage[main]/Main/Notify[Benchmark Test]/message: defined 'message' as 'Running Puppet standalone benchmark.'
Notice: Applied catalog in 0.01 seconds

real    0m1.175s
user    0m0.779s
sys     0m0.385s
```

### Benchmark Metrics Explanation

- **Compiled catalog** → Puppet compiled your manifest into an execution plan.  
- **Applied catalog** → Puppet executed the plan on your system.  
- **real** → Total elapsed wall time (includes CPU + I/O).  
- **user** → CPU time spent in user-space.  
- **sys** → CPU time spent in system calls.  

### Benchmark summary on x86_64
To compare the benchmark results, the following results were collected by running the same benchmark on a `x86 - c4-standard-4` (4 vCPUs, 15 GB Memory) x86_64 VM in GCP, running SUSE:


### Benchmark summary on Arm64
Results from the earlier run on the `c4a-standard-4` (4 vCPU, 16 GB memory) Arm64 VM in GCP (SUSE):

| **Metric / Log** | **Output** |
|-------------------|------------|
| Compiled catalog | lpprojectsusearm64.c.imperial-time-463411-q5.internal in environment production in 0.01 seconds |
| Environment | production |
| Configuration version | 1762160712 |
| Benchmark message | Running Puppet standalone benchmark |
| Notify resource | /Stage[main]/Main/Notify[Benchmark Test]/message: defined 'message' as 'Running Puppet standalone benchmark.' |
| Applied catalog | 0.01 seconds |
| real | 0m1.175s |
| user | 0m0.779s |
| sys | 0m0.385s |

### Puppet benchmarking comparison on Arm64 and x86_64

