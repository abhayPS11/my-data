---
title: Install Django
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Django on GCP VM

### Update Your System
Before installing Django, ensure your system is up to date. Updating packages ensures compatibility and security.

```console
sudo zypper refresh
sudo zypper update -y
```

### Install Python and Tools
**Django** requires **Python**. You also need `pip` for Python package management, and `git`, `gcc`, and `make` for development tools.
Make sure all of these are installed before proceeding with your Django setup.

```console
sudo zypper install -y python311 python311-pip python311-devel 
sudo zypper install -y git gcc make
```

**Verify the installation:**

```console
python3 --version
pip3 --version
```

You should see an output similar to:
```output
Python 3.11.13
pip 22.3.1 from /usr/lib/python3.11/site-packages/pip (python 3.11)
```

### Create a Project Folder and Virtual Environment

```console
mkdir ~/myproject && cd ~/myproject
python3 -m venv venv
source venv/bin/activate
```
Your prompt should now show `(venv)`.

### Upgrade Pip and Install Django

```console
pip3 install --upgrade pip
pip3 install django
```

Check:

```console
django-admin --version
```

You should see an output similar to:
```output
5.2.7
```




