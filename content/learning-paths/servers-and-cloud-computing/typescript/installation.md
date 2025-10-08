---
title: Install TypeScript
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install TypeScript on GCP VM

```console
sudo zypper refresh
sudo zypper update -y
```
### Install Node.js and npm

```console
sudo zypper install -y nodejs npm
```
### Install TypeScript globally

```console
sudo npm install -g typescript ts-node
```

### Verify installations

```console
node -v
npm -v
tsc -v
ts-node -v
```

```output
>node -v
v18.20.5
>npm -v
10.8.2
>tsc -v
Version 5.9.3
> ts-node -v
v10.9.2
```


