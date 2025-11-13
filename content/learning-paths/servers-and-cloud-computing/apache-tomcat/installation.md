---
title: Install Apache Tomcat 
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Apache Tomcat Installation on GCP SUSE VM

### Update System Packages
Refresh and update your SUSE VM to ensure all dependencies are current.

```console
sudo zypper refresh
sudo zypper update -y
```
Keeps your system up-to-date and installs essential package metadata.

### Install Java 17
Tomcat 11 requires Java 17 or newer. Install OpenJDK 17 using zypper.

```console
sudo zypper install -y java-17-openjdk
```

**Verify installation:**

```console
java -version
```

```output
openjdk version "17.0.16" 2025-07-15
OpenJDK Runtime Environment (build 17.0.16+8-suse-150400.3.57.1-aarch64)
OpenJDK 64-Bit Server VM (build 17.0.16+8-suse-150400.3.57.1-aarch64, mixed mode, sharing)
```

### Download Apache Tomcat
Fetch the official release tarball from the Apache archive.

```console
wget https://archive.apache.org/dist/tomcat/tomcat-11/v11.0.7/bin/apache-tomcat-11.0.7.tar.gz
```

### Extract the Tomcat Archive
Unpack the tarball and move it to `/opt/` for system-wide use.

```console
tar -xvzf apache-tomcat-11.0.7.tar.gz
sudo mv apache-tomcat-11.0.7 /opt/tomcat
```
Extracts Tomcat files and places them in a standard directory.

### Configure Environment Variables
Add Tomcat to your shell environment.

```console
echo 'export CATALINA_HOME=/opt/tomcat' >> ~/.bashrc
source ~/.bashrc
```
Set the Tomcat home directory for easy command access.

### Start the Tomcat Server
Navigate to Tomcat’s `bin/` directory and start the server.

```console
cd $CATALINA_HOME/bin
./startup.sh
```

```output
Using CATALINA_BASE:   /opt/tomcat
Using CATALINA_HOME:   /opt/tomcat
Using CATALINA_TMPDIR: /opt/tomcat/temp
Using JRE_HOME:        /usr/lib64/jvm/java-17-openjdk-17
Using CLASSPATH:       /opt/tomcat/bin/bootstrap.jar:/opt/tomcat/bin/tomcat-juli.jar
Using CATALINA_OPTS:
Tomcat started.
```

### Verify Installation
Since you are already in the Tomcat bin directory (`/opt/tomcat/bin`), you can check the version by the following command:

```console
./catalina.sh version
```

```output
Using CATALINA_BASE:   /opt/tomcat
Using CATALINA_HOME:   /opt/tomcat
Using CATALINA_TMPDIR: /opt/tomcat/temp
Using JRE_HOME:        /usr/lib64/jvm/java-17-openjdk-17
Using CLASSPATH:       /opt/tomcat/bin/bootstrap.jar:/opt/tomcat/bin/tomcat-juli.jar
Using CATALINA_OPTS:
Server version: Apache Tomcat/11.0.7
Server built:   May 7 2025 14:55:59 UTC
Server number:  11.0.7.0
OS Name:        Linux
OS Version:     6.4.0-150600.23.73-default
Architecture:   aarch64
JVM Version:    17.0.16+8-suse-150400.3.57.1-aarch64
JVM Vendor:     Oracle Corporation
```


