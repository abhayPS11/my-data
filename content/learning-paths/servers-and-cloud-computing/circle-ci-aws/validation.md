---
title: Verify CircleCI ARM64 Self-Hosted Runner
weight: 7

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Verify CircleCI ARM64 Self-Hosted Runner

This guide demonstrates how to validate your **self-hosted CircleCI runner** on an **ARM64 machine** by running a simple workflow and a sample computation.

---

### Create a Test Repository

Clone a new GitHub repository for Arm64 runner verification:

```console
git clone https://github.com/<your_repo_name/aws-circleci/
```

### Add a Sample Script

Create a simple shell script to test the runner:

```console
echo 'echo "Hello from CircleCI ARM64 Runner!"' > hello.sh
chmod +x hello.sh
```

### Define the CircleCI Configuration
Create a `.circleci/config.yml` file with the following content:

```yaml
version: 2.1

jobs:
  test-arm64:
    machine:
      enabled: true
    resource_class: your-namespace/arm64-linux   # Replace with your actual resource class
    steps:
      - checkout
      - run:
          name: Verify ARM64 Runner
          command: |
            uname -m
            lscpu | grep Architecture
            ./hello.sh
      - run:
          name: Run sample computation
          command: |
            echo "Performing test on ARM64 runner"
            echo "CPU Info:" 
            lscpu
            echo "Success!"

workflows:
  test-workflow:
    jobs:
      - test-arm64
```

### Commit and Push to GitHub
Push your local repository to GitHub:

```console
git add .
git commit -m "Initial CircleCI ARM64 test"
git branch -M main
git push -u origin main
```

- **Add Changes**: Stage all modified and new files using `git add .`.
- **Commit Changes**: Commit the staged files with a descriptive message.
- **Set Main Branch**: Rename the current branch to `main`.
- **Add Remote Repository**: Link your local repository to GitHub.
- **Push Changes**: Push the committed changes to the `main` branch on GitHub.

### Start CircleCI Runner and Execute Job
Ensure that your CircleCI runner is enabled and started. This will allow your self-hosted runner to pick up jobs from CircleCI.

```console
sudo systemctl enable circleci-runner
sudo systemctl start circleci-runner
sudo systemctl status circleci-runner
```
- **Enable CircleCI Runner**: Ensure the CircleCI runner is set to start automatically on boot.
- **Start and Check Status**: Start the CircleCI runner and verify it is running.

After pushing your code to GitHub, open your **CircleCI Dashboard → Projects**, and confirm that your **test-arm64 workflow** starts running using your **self-hosted runner**.

If the setup is correct, you’ll see your job running under the resource class you created.

### Output
Once the job starts running, CircleCI will:

- Verify ARM64 Runner:
- Run sample computation:

If everything is set up correctly, your CircleCI job will execute, and the application will be deployed, visible in the CircleCI Dashboard.
