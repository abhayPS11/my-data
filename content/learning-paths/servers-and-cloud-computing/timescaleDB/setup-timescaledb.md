---
title: TimescaleDB Environment Setup on ARM64
weight: 1

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## TimescaleDB Environment Setup

In this guide, you will prepare an ARM64-based SUSE Linux Enterprise Server (SLES) virtual machine and install TimescaleDB by building it from source.

Building from source ensures maximum performance and ARM-native optimization.

## Architecture Overview

```text
Linux ARM64 VM (SUSE)
        |
        v
PostgreSQL 15
        |
        v
TimescaleDB 2.25.0 Extension
```

TimescaleDB provides time-series optimizations on top of PostgreSQL, making it ideal for high-ingest sensor workloads.

## Install Build Dependencies (SUSE)

TimescaleDB requires PostgreSQL development headers and build tools.

```bash
sudo zypper refresh
```

```bash
sudo zypper install \
  cmake \
  gcc gcc-c++ make \
  git \
  libopenssl-devel \
  postgresql15 \
  postgresql15-server \
  postgresql15-devel
```

### Important (SUSE note)
If prompted about readline-devel, choose Solution 1 (vendor change/downgrade).

## Initialize PostgreSQL

```bash
sudo -u postgres initdb -D /var/lib/pgsql/data
```

**Enable and start PostgreSQL:**

```bash
sudo systemctl enable postgresql
sudo systemctl start postgresql
```


**Verify PostgreSQL:**

```bash
psql --version
```

```output
psql (PostgreSQL) 15.10
```

## Build TimescaleDB from Source (ARM64)

### Clone the repository

```bash
git clone https://github.com/timescale/timescaledb.git
cd timescaledb
git checkout 2.25.0
```

### Bootstrap and build:

```bash
./bootstrap
cd build
make -j$(nproc)
sudo make install
```

This compiles TimescaleDB natively for Arm64.

## Enable TimescaleDB in PostgreSQL

## Edit PostgreSQL configuration

```bash
sudo vi /var/lib/pgsql/data/postgresql.conf
```

**Add:**

```text
shared_preload_libraries = 'timescaledb'
```

### Restart PostgreSQL

```bash
sudo systemctl restart postgresql
```

## Create Database and Enable Extension

```bash
sudo -u postgres psql
```

```psql
CREATE DATABASE sensors;
\c sensors
CREATE EXTENSION IF NOT EXISTS timescaledb;
```

**Verify version:**

```psql
SELECT extversion FROM pg_extension WHERE extname='timescaledb';
```
```output
sensors=# SELECT extversion FROM pg_extension WHERE extname='timescaledb';
 extversion
------------
 2.25.0
(1 row)
```

## What You Have Accomplished

- Installed PostgreSQL 15 on SUSE ARM64
- Built TimescaleDB 2.25.0 from source
- Enabled TimescaleDB at database level

## What’s Next

In the next section, you will create a real-time sensor ingestion pipeline using Python.
