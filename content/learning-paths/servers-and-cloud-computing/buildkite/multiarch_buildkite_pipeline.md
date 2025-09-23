---
title: Microservice/App with Multi-Arch Builds
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Multi-Arch with Buildkite

After you have installed **Buildkite** and **Docker**, you can set up a simple Python microservice to test **multi-architecture builds**.

Make sure you have a GitHub repository ready where you can execute the upcoming steps, including creating the Dockerfile, app.py, and your pipeline.yml.

### Create Dockerfile

Inside your GitHub repo, add a file named `Dockerfile` with this content:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY app.py .

RUN pip install flask

EXPOSE 5000

CMD ["python", "app.py"]
```

### Create app.py
This Python code defines a simple Flask web server that listens on all interfaces (0.0.0.0) at port 5000 and responds with "Hello from Arm-based Buildkite runner!" when the root URL (/) is accessed.
It is essentially a basic “Hello World” web app running on an ARM-based environment.

In the same repo, add a file named `app.py`:

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Arm-based Buildkite runner!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

### Push Repo to GitHub

Commit and push your repo with both `Dockerfile` and `app.py`.

### Add Deploy Key for Buildkite Agent

On your SUSE Arm virtual machine, generate SSH key for your agent user:

```console
ssh-keygen -t rsa -b 4096 -C "buildkite-agent" -f ~/.ssh/id_rsa
```
Add Deploy Key for Buildkite Agent

Copy the public key:

```console
cat ~/.ssh/id_rsa.pub
```
Now go to your GitHub repository:

1. Navigate to **Settings → Deploy Keys → Add Key**
2. Paste the copied public key
3. Enable **“Allow write access”**

This allows your Buildkite agent to securely pull from the GitHub repository.

### Create Buildkite Pipeline for Multiarch

In Buildkite, define your pipeline YAML (through the UI):

1. Go to **Buildkite Dashboard → Pipelines → New Pipeline**.

![Buildkite Dashboard alt-text#center](images/pipeline.png "Figure 1: Create Pipeline")

2. Fill the form:

   - **Git Repository** → your repo URL (SSH or HTTPS)  

3. In the **Steps (YAML Steps)** section, paste your pipeline YAML.

![Buildkite Dashboard alt-text#center](images/yaml.png "Figure 3: YAML steps")
4. Click **Create Pipeline**.

5. Trigger a new build by clicking **New Build** on your pipeline’s dashboard.


```yaml
steps:
  - label: "Build and Push Multiarch App"
    env:
      DOCKER_CONFIG: "~/.docker"
    commands:
      - echo "Testing env hook..."
      - env | grep DOCKER
      - ~/.buildkite-agent/bin/buildkite-agent secret get "DOCKER_PASSWORD" | docker login -u "$(~/.buildkite-agent/bin/buildkite-agent secret get "DOCKER_USERNAME")" --password-stdin
      - docker buildx rm mybuilder || true
      - docker buildx create --use --name mybuilder
      - docker buildx inspect --bootstrap
      - docker buildx build --platform linux/amd64,linux/arm64 -t "$(~/.buildkite-agent/bin/buildkite-agent secret get "DOCKER_USERNAME")/multi-arch-app:latest" --push . 
    agents:
      queue: buildkite-queue1
```

{{% notice Note %}}
Make sure to add your Docker credentials as secrets in the Buildkite UI.
- Navigate to: **Buildkite → Agents → Secrets**
- Here you can add `DOCKER_USERNAME` and `DOCKER_PASSWORD`.

These will be fetched automatically when your pipeline YAML is triggered.
{{% /notice %}}

![Buildkite Dashboard alt-text#center](images/secrets.png "Figure 2: Set Secrets")

Once your files and pipeline are ready, you can validate that your Buildkite agent is running and ready to execute jobs.
