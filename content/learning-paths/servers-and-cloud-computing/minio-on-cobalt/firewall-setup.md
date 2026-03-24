---
title: Create firewall rules on GCP for MinIO
weight: 3

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Configure GCP firewall for MinIO
To allow inbound traffic for MinIO, create a firewall rule in the Google Cloud Console. This firewall rule enables access to MinIO object storage on Axion (arm64) virtual machines.

{{% notice Note %}}
For more information about GCP setup, see [Getting started with Google Cloud Platform](/learning-paths/servers-and-cloud-computing/csp/google/).
{{% /notice %}}

## Required ports

| Service | Port | Purpose |
| ------- | ---- | ------- |
| MinIO S3 API | 9000 | S3-compatible object storage access |
| MinIO Web UI | 9001 | Bucket management and object browsing |

## Create a firewall rule in GCP

To expose the TCP ports listed above, create a firewall rule.

Navigate to the [Google Cloud Console](https://console.cloud.google.com/), go to **VPC Network > Firewall**, and select **Create firewall rule**.

![Google Cloud Console VPC Network Firewall page showing existing firewall rules and Create Firewall Rule button alt-txt#center](images/firewall-rule1.png "Create a firewall rule")

Next, create the firewall rule that exposes the required TCP ports.  
Set the **Name** of the new rule to `allow-minio`. Select the network you intend to bind to your VM (the default is `default`, but your organization may use a different one).

Set **Direction of traffic** to "Ingress". Set **Allow on match** to "Allow" and **Targets** to "Specified target tags".

![Google Cloud Console firewall rule creation form showing name field, network selection, direction set to Ingress, and targets set to Specified target tags alt-txt#center](images/network-rule2.png "Creating Arrow firewall rule")

Next, enter `allow-minio` in the **Target tags** field. Set **Source IPv4 ranges** to `0.0.0.0/0`.

![Google Cloud Console firewall rule form showing target tags field with allow-minio entered and source IPv4 ranges set to 0.0.0.0/0 alt-txt#center](images/network-rule3.png "Creating the Arrow and MinIO firewall rule")

Finally, select **Specified protocols and ports** under the **Protocols and ports** section. Select the **TCP** checkbox, enter `9000,9001` in the **Ports** field, and select **Create**.

![Google Cloud Console firewall rule form showing protocols and ports section with TCP selected and ports 9000,9001 specified alt-txt#center](images/network-port.png "Specifying TCP ports for Apache Arrow and MinIO")

## What you've learned and what's next

You've successfully:

- Created firewall rules in Google Cloud to expose ports for Apache Arrow analytics components
- Enabled external access to MinIO S3 API and Web UI

Next, you'll provision a Google Axion C4A Arm virtual machine and deploy MinIO object storage on it.
