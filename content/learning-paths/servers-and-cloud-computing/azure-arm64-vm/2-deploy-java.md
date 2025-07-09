---
title: Install the JDK and build an application
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---


### Working Inside Azure Linux 3.0 (Docker Container or Custom VM)

Azure Linux 3.0 is a Microsoft-maintained operating system built on CBL-Mariner, an open-source Linux distribution optimized for Azure workloads, including use on Azure Kubernetes Service (AKS) as a container host.

To learn more, refer to the [official Microsoft documentation](https://learn.microsoft.com/en-us/azure/azure-linux/intro-azure-linux). Azure Linux 3.0 supports the AArch64 (Arm64) architecture. However, a standalone VM image of Azure Linux 3.0 or CBL-Mariner 3.0 for Arm64 is not publicly available.

### Create Azure Linux 3.0 Docker Container 
The Microsoft Artifact Registry offers updated docker image for the Azure Linux 3.0. Kindly find the details of [Azure Linux Base image](https://mcr.microsoft.com/en-us/artifact/mar/azurelinux/base/core/about).
To create a docker container, install docker, and then follow the below instructions: 

```console
$ sudo docker run -it --rm mcr.microsoft.com/azurelinux/base/core:3.0
``` 

The default container startup command is bash. tdnf and dnf are the default package managers.

### Install Java

This Azure Linux 3.0 image does not include Java, so you need to install it. 

{{% notice Note %}}
The installation, and validation commands are identical for both setups. 
{{% /notice %}}

First update tdnf:

```console
$ tdnf update -y 
``` 
Then install java-devel:

```console
$ tdnf install -y java-devel  
```

Java-devel installs both the default JRE and JDK provided by Azure Linux 3.0.

Check to ensure that the JRE is properly installed: 

```console
$ java -version 
``` 

**Your output will look like this:** 

```output
openjdk version "11.0.27" 2025-04-15 LTS 
OpenJDK Runtime Environment Microsoft-11371464 (build 11.0.27+6-LTS) 
OpenJDK 64-Bit Server VM Microsoft-11371464 (build 11.0.27+6-LTS, mixed mode, 
sharing) 
```

**Check to ensure that the JDK is properly installed:**

```console
$ javac -version 
```
Your output will look like this:

```output
javac 11.0.27 
```

Set Java Environment Variable for Arm: 

```bash { line_numbers = “true” }
$ export JAVA_HOME=/usr/lib/jvm/msopenjdk-11 
$ export PATH=$JAVA_HOME/bin:$PATH 
```

{{% notice Note %}}
Azure Linux 3.0 offers the default JDK version 11.0.27. It’s important to ensure that your version of OpenJDK for ARM is at least 11.0.9, or above. There is a large performance gap between OpenJDK-11.0.8 and OpenJDK 11.0.9. A patch added in 11.0.9 reduces false-sharing cache contention. 
For more information, you can view this [ARM community blog](https://community.arm.com/arm-community-blogs/b/architectures-and-processors-blog/posts/java-performance-on-neoverse-n1). 

The [ARM Ecosystem Dashboard](https://developer.arm.com/ecosystem-dashboard/) also recommends Java/OpenJDK version 11.0.9 as minimum recommended on the ARM platforms.
{{% /notice %}}

### Deploy a Java application with Tomcat-like operation 
Apache Tomcat is a Java-based web application server (technically, a Servlet container) that executes Java web applications. It's widely used to host Java servlets, JSP (JavaServer Pages), 
and RESTful APIs written in Java. 
The below Java class simulates the generation of a basic HTTP response and measures the time taken to construct it, mimicking a lightweight Tomcat-like operation. It measures how long it 
takes to build the response string, helping evaluate raw Java execution efficiency before deploying heavier frameworks like Tomcat.
Create a file named `HttpSingleRequestTest.java`, and add the below content to it:

```
public class HttpSingleRequestTest {
    public static void main(String[] args) {
        long startTime = System.nanoTime();
        String response = generateHttpResponse("Tomcat baseline test on ARM64");
        long endTime = System.nanoTime();
        double durationInMicros = (endTime - startTime) / 1_000.0;
        System.out.println("Response Generated:\n" + response);
        System.out.printf("Response generation took %.2f microseconds.%n", durationInMicros);
    }
    private static String generateHttpResponse(String body) {
        return "HTTP/1.1 200 OK\r\n" +
               "Content-Type: text/plain\r\n" +
               "Content-Length: " + body.length() + "\r\n\r\n" +
               body;
    }
}
```
Compile and Run Java program :

```console
$ javac HttpSingleRequestTest.java
$ java -Xms128m -Xmx256m -XX:+UseG1GC HttpSingleRequestTest
```

- -Xms128m  sets the initial heap size for the Java Virtual Machine to 128 MB. 
- -Xmx256m sets the maximum heap size for the JVM to 256 MB. 
- -XX:+UseG1GC enables the G1 Garbage Collector (Garbage First GC), designed for low pause times and better performance in large heaps.

Output of java program on the ARM :
```output
$ javac HttpSingleRequestTest.java
$ java -Xms128m -Xmx256m -XX:+UseG1GC HttpSingleRequestTest
Response Generated:
HTTP/1.1 200 OK
Content-Type: text/plain
Content-Length: 29
Tomcat baseline test on ARM64
Response generation took 22125.79 microseconds.
```
Output summary:

- The program generated a fake HTTP 200 OK response with a custom message.
- It then measured and printed the time taken to generate that response (22125.79 microseconds).
- This serves as a basic baseline performance test of string formatting and memory handling on the JVM running on an Azure Arm64 instance.

