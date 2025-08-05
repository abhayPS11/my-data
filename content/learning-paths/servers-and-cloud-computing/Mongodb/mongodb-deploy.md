---
title: Install MongoDb on Google Axion C4A virtual machine
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## Install MongoDB and mongosh on Google Axion C4A virtual machine

Install MongoDB and mongosh on GCP RHEL 9 Arm64 by downloading the binaries, setting up environment paths, configuring data and log directories, and starting the server for local access and verification.

1️. Install System Dependencies

Install required system packages to support MongoDB.
```console
sudo dnf install -y libcurl openssl tar wget curl
```

2️. Download annd Extract MongoDB

Fetch and unpack the MongoDB binaries for ARM64.
```console
wget https://fastdl.mongodb.org/linux/mongodb-linux-aarch64-rhel93-8.0.12.tgz
tar -xzf mongodb-linux-aarch64-rhel93-8.0.12.tgz
sudo mv mongodb-linux-aarch64-rhel93-8.0.12 /usr/local/mongodb
```

3️. Add MongoDB to System PATH

Enable running mongod from any terminal session.
```console
echo 'export PATH=/usr/local/mongodb/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

4️. Create Required Directories

Set up the database data directory with correct permissions.
```console
sudo mkdir -p /data/db
sudo chown $USER /data/db
```

5️. Create Log Directory

Ensure MongoDB has a writable log path to avoid startup failure.
```console
mkdir -p ~/mongod-logs
touch ~/mongod-logs/mongod.log
```

6️. Start MongoDB Server 

Launch MongoDB without forking to view real-time errors.
```console
mongod --dbpath /data/db \
       --bind_ip 127.0.0.1 \
       --port 27017 \
       --logpath ~/mongod-logs/mongod.log
```
7️. Install mongosh (MongoDB Shell for Arm)

Download and install MongoDB’s command-line shell for Arm
```console
wget https://downloads.mongodb.com/compass/mongosh-2.2.5-linux-arm64.tgz
tar -xzf mongosh-2.2.5-linux-arm64.tgz
sudo mv mongosh-2.2.5-linux-arm64/bin/mongosh /usr/local/bin/
```

### Verify Mongodb and mongosh Installation

Check if MongoDb and mongosh is properly installed.
```console
mongod --version
mongosh --version
```
You should see an output similar to: 
```output
db version v8.0.12
Build Info: {
    "version": "8.0.12",
    "gitVersion": "b60fc6875b5fb4b63cc0dbbd8dda0d6d6277921a",
    "openSSLVersion": "OpenSSL 3.2.2 4 Jun 2024",
    "modules": [],
    "allocator": "tcmalloc-google",
    "environment": {
        "distmod": "rhel93",
        "distarch": "aarch64",
        "target_arch": "aarch64"
    }
}
[gcpuser@lpprojectrhel9arm64 ~]$ mongosh --version
2.5.6
```

### Connect to MongoDB via mongosh

Start interacting with MongoDB through its shell interface.
```console
mongosh mongodb://127.0.0.1:27017
```
You should see an output similar to: 
```output
[gcpuser@lpprojectrhel9arm64 ~]$ mongosh mongodb://127.0.0.1:27017
Current Mongosh Log ID: 6891ebb158db5b705d74e399
Connecting to:          mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.6
Using MongoDB:          8.0.12
Using Mongosh:          2.5.6

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/

------
   The server generated these startup warnings when booting
   2025-08-05T07:17:45.864+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
   2025-08-05T07:17:45.864+00:00: Soft rlimits for open file descriptors too low
   2025-08-05T07:17:45.864+00:00: For customers running the current memory allocator, we suggest changing the contents of the following sysfsFile
   2025-08-05T07:17:45.864+00:00: We suggest setting the contents of sysfsFile to 0.
   2025-08-05T07:17:45.864+00:00: Your system has glibc support for rseq built in, which is not yet supported by tcmalloc-google and has critical performance implications. Please set the environment variable GLIBC_TUNABLES=glibc.pthread.rseq=0
------

test>
```

MongoDB installation is complete. You can now proceed with the baseline testing.
