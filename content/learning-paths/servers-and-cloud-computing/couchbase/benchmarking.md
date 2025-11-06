---
title: Couchbase Benchmarking
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


##  Puppet Benchmark on GCP SUSE Arm64 VM

### Install Build Tools & Dependencies

```console
sudo zypper install -y gcc gcc-c++ cmake make git openssl-devel libevent-devel cyrus-sasl-devel
```

### Download and Build the Couchbase C SDK (includes cbc-pillowfight)

```console
cd ~
git clone https://github.com/couchbase/libcouchbase.git
cd libcouchbase
```

**Then build and install:**

```console
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)
sudo make install
```

### Update the Dynamic Linker Configuration
Tell the OS where to look for the library:

```console
echo "/usr/local/lib" | sudo tee /etc/ld.so.conf.d/libcouchbase.conf
```

Then refresh the linker cache:

```console
sudo ldconfig
```

### Verify Installation
After installation, the tools like **cbc**, **cbc-pillowfight**, etc. should be available in `/usr/local/bin`.

**Verify with:**

```console
cbc --version
cbc-pillowfight --help
```

### Run Benchmark using cbc-pillowfight
Once Couchbase Server is running and a bucket (e.g., `benchmark`) is created, you can run a workload test using the following command:

```console
cbc-pillowfight -U couchbase://127.0.0.1/benchmark \
-u Administrator -P password \
-I 10000 -B 1000 -t 5 -c 500
```

- **-U couchbase://127.0.0.1/benchmark**: Connection string to Couchbase bucket
- **-u Administrator**:	Couchbase username
- **-P password**: Couchbase password
- **-I 10000**:	Number of items (documents) to use
- **-B 1000**: Batch size for operations
- **-t 5**:	Number of concurrent threads
- **-c 500**:	Number of operation cycles to run

### Monitoring During Test
While running `cbc-pillowfight`, open the Couchbase Web UI at:

```bash
http://<your-vm-ip>:8091
```

Go to:
**Dashboard → Buckets → benchmark → Metrics tab**

Watch these in real time:
- **Ops/sec** — should match your CLI output
- **Resident ratio** — how much data stays in memory
- **Disk write queue** — backlog of writes to disk
- **CPU and memory usage** — tells you how well ARM cores are handling load

![Couchebase Dashboard alt-text#center](images/arm-benchmark.png "Monitor Benchmark Log")

### Benchmark summary on x86_64
To compare the benchmark results, the following results were collected by running the same benchmark on a `x86 - c4-standard-4` (4 vCPUs, 15 GB Memory) x86_64 VM in GCP, running SUSE:

| **Name**     | **Items** | **Resident** | **Ops/sec** | **RAM Used / Quota** | **Disk Used** |
|---------------|------------|---------------|---------------|-----------------------|---------------|
| benchmark     | 10,000     | 100%          | 219,961.9     | 36.7 MiB / 1 GiB      | 2.37 MiB      |

### Benchmark summary on Arm64
Results from the earlier run on the `c4a-standard-4` (4 vCPU, 16 GB memory) Arm64 VM in GCP (SUSE):

| **Name**     | **Items** | **Resident** | **Ops/sec** | **RAM Used / Quota** | **Disk Used** |
|---------------|------------|---------------|---------------|-----------------------|---------------|
| benchmark     | 10,000     | 100%          | 227,981.1     | 36.8 MiB / 1 GiB      | 26.7 MiB      |


