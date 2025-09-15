---
title: Set-up Buildkite
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

# Buildkite Agent Setup 
This guide describes the steps to configure a Buildkite agent and queue after installing the Buildkite agent binary on a SUSE ARM64 VM.

## 1. Create an Agent Token

Before configuring the agent, you need an agent token from your Buildkite organization.

1. Log in to your **Buildkite** account and go to your **Organization Settings**.  
2. Click **Agents** in the left menu.  
3. Click **Create Agent Token**.  
4. Enter a **name** for your token, e.g., `buildkite-arm`.  
5. Click **Create Token**.  
6. **Copy the token** immediately – you won’t be able to see it again after leaving the page.


## 2. Configure Buildkite Agent

Create the configuration directory and file on your SUSE ARM64 VM:

```bash
sudo mkdir -p /etc/buildkite-agent
sudo tee /etc/buildkite-agent/buildkite-agent.cfg > /dev/null <<EOF
token="YOUR_AGENT_TOKEN"
name="buildkite-arm"
tags="queue=buildkite-queue"
EOF
