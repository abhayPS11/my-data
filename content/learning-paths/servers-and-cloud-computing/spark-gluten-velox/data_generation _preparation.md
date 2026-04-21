---
title: TPC-DS Benchmarking on Arm (Part 2 - Data Generation)
weight: 2

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Generate and Prepare TPC-DS Dataset

## Objective

In this part, you will:

* Build TPC-DS kit
* Generate 10GB dataset
* Prepare data for benchmarking
* Convert data to Parquet format


## Install TPC-DS Kit

```console
cd /opt
git clone https://github.com/gregrahn/tpcds-kit.git
cd tpcds-kit/tools
make
```

