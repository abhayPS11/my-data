---
title: Jenkins Use Case 2 – Docker-based CI Pipeline on ARM64
weight: 6 

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Jenkins Use Case – Docker-based CI Pipeline on Arm64
This use case demonstrates how to use **Jenkins on a GCP SUSE Arm64 VM** to build and run a **Docker container natively on ARM64**. It validates Docker installation, Jenkins–Docker integration, and ARM-native container execution.

### Prerequisites

* Jenkins is installed and running on a GCP SUSE Arm64 VM
* Jenkins web UI accessible
* Docker installed on the VM
* Jenkins user added to Docker group

### Install Docker on SUSE Linux (ARM64)

```bash
sudo zypper refresh
sudo zypper install -y docker
```

### Enable and start Docker service

```console
sudo systemctl enable docker
sudo systemctl start docker
```

### Allow Jenkins to Use Docker
Jenkins runs as the jenkins user and must have permission to execute Docker commands.

**Add Jenkins user to Docker group:**

```console
sudo usermod -aG docker jenkins
```

### Restart services

```console
sudo systemctl restart docker
sudo systemctl restart jenkins
```

### Verify Docker access as Jenkins

```console
sudo -u jenkins docker version
```

### Prepare Jenkins Workspace
All files must be created as the Jenkins user inside the Jenkins workspace.

### Switch to Jenkins user

```console
sudo -u jenkins bash
```

### Navigate to Jenkins job workspace

```console
cd /var/lib/jenkins/workspace/docker-arm-ci
```

`docker-arm-ci` must match your Jenkins job name.

### Create Docker demo directory

```console
mkdir docker-demo
cd docker-demo
```

### Create ARM64 Dockerfile

```bash
cat <<EOF > Dockerfile
FROM arm64v8/alpine:latest
CMD ["echo", "Hello from ARM64 Docker container"]
EOF
```

**Dockerfile details:**

- Uses an ARM64-native base image
- Prints a message when the container runs

### Exit Jenkins shell

```console
exit
```

### Create Jenkins Pipeline Job

#### Step 1: Open Jenkins UI

```console
http://<VM_PUBLIC_IP>:8080
```

#### Step 2: Create a new Pipeline job

* Open Jenkins UI
* Click **New Item**
* Job name: `docker-arm-ci`
* Select **Pipeline**
* Click **OK**

![ Jenkins UI alt-text#center](images/jenkins-go.png "Figure 4: Create Job")

#### Step 3: Jenkins Pipeline Script (Docker ARM Validation)

Scroll to the **Pipeline** section and select:

**Definition:** Pipeline script

Paste the following into the Pipeline script section:

```groovy
pipeline {
  agent any

  stages {
    stage('Environment Check') {
      steps {
        sh 'uname -m'
        sh 'docker version'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh '''
          cd docker-demo
          docker build -t arm64-docker-test .
        '''
      }
    }

    stage('Run Docker Container') {
      steps {
        sh '''
          docker run --rm arm64-docker-test
        '''
      }
    }
  }
}
```

Click **Save**.

#### step 4: Execute the Pipeline
* On the job page, click **Build Now**
* Click the build number
* Open **Console Output**
  

#### step 4: View console output

Review the pipeline logs to confirm successful execution.

1. Click the build number (for example, `#1`)
2. Click **Console Output**



This confirms:

Jenkins is running on ARM64

Docker is ARM-native

Jenkins can build and run containers

End-to-end Docker CI works on ARM

8. Use Case 2 Baseline Summary

This use case validates Docker-based CI pipelines using Jenkins on a GCP SUSE ARM64 VM.
Docker installation, Jenkins–Docker integration, ARM-native image builds, and container execution are successfully verified.
The system is now ready for ARM-native containerized CI/CD workloads.
