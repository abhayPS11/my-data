---
title: Apache Tomcat Baseline Testing on Google Axion C4A Arm Virtual Machine
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Baseline Testing for Apache Tomcat
This section ensures Tomcat is installed correctly and responding as expected before running full benchmarks.

### Start Tomcat Server

```console
cd /opt/tomcat/bin
./catalina.sh start
```
- Starts Tomcat in the background.
- Check running processes:

  ```console
  ps -ef | grep tomcat
  ```

### Access Tomcat in Browser
Open your browser and visit:

```console
http://<VM_EXTERNAL_IP>:8080
```
You should see the Apache Tomcat default welcome page.

### Local curl Test
Run a quick local test to confirm HTTP response:

```console
curl -I http://localhost:8080
```

```output
HTTP/1.1 200
Content-Type: text/html;charset=UTF-8
Date: Thu, 13 Nov 2025 10:36:28 GMT
```



