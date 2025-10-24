---
title: Install Redis
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Redis on GCP VM

### Prerequisites
Before building Redis, ensure your system is up-to-date and the required tools are installed.

```console
sudo zypper refresh
sudo zypper install -y gcc gcc-c++ make tcl openssl-devel wget
```
### Download and Extract Redis Source Code

```console
wget https://github.com/redis/redis/archive/refs/tags/8.2.2.tar.gz
tar -xvf 8.2.2.tar.gz
cd redis-8.2.2
```

### Build Redis with TLS Support
Clean any previous build artifacts (if any):

```console
make distclean
```

Now build Redis dependencies and compile Redis:

```console
cd deps
sudo make hiredis jemalloc linenoise lua fast_float
cd ..
sudo make BUILD_TLS=yes
```
Note: The BUILD_TLS=yes flag enables TLS (SSL) support for secure Redis connections.

### Verify Redis Binary
After a successful build, check that the redis-server binary exists:

```
cd src
ls -l redis-server
```

You should see a file similar to:

```output
-rwxr-xr-x 1 root root 17869216 Oct 23 11:48 redis-server
```

### Install Redis System-Wide
To make redis-server and redis-cli accessible globally:

```console
sudo make install
```

Verify installation paths:

```console
which redis-server
which redis-cli
```


Expected:

```output
/usr/local/bin/redis-server
/usr/local/bin/redis-cli
```

### Verify Installation
Check Redis versions:

```console
redis-server --version
redis-cli --version
```

```output
gcpuser@lpprojectsusearm64:~> redis-server --version
Redis server v=8.2.2 sha=00000000:1 malloc=jemalloc-5.3.0 bits=64 build=72ba144d8c96c783
gcpuser@lpprojectsusearm64:~> redis-cli --version
redis-cli 8.2.2
```
