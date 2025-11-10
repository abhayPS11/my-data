---
title: Install Rust
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Rust Installation on GCP SUSE VM

### System Preparation
Update your system packages and install basic tools:

```console
sudo zypper refresh
sudo zypper update -y
sudo zypper install -y curl gcc make
```
### Install Rust Using rustup
Rust provides an official installer script via `rustup`, which handles the setup automatically:

```console
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
When prompted, choose **option 1** (default installation).

### Configure Rust Environment
After installation, activate the Rust environment for your session:

```console
source $HOME/.cargo/env
```

### Verify Rust Installation
Check if Rust and Cargo (Rust’s package manager) are properly installed:

```console
rustc --version
cargo --version
```

You should see an output similar to:
```output
rustc 1.91.0 (f8297e351 2025-10-28)
cargo 1.91.0 (ea2d97820 2025-10-10)
```

