---
title: Install Golang on Microsoft Azure Virtual Machine
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## Install Golang on Azure Linux 3.0
This guide covers installing the latest Go version on Azure Linux Arm64, configuring the environment, and verifying the setup.

1. Download the Go archive

This command downloads the official Go package for Linux Arm64 from the Go website.

```console
wget https://go.dev/dl/go1.25.0.linux-arm64.tar.gz
```
{{% notice Note %}}
According to the [Arm Ecosystem Dashboard](https://developer.arm.com/ecosystem-dashboard/), the minimum recommended version of Golang for Arm platforms is 1.5. Therefore, you can use any version starting from 1.5 up to the latest release.
{{% /notice %}}

2. Extract the archive into `/usr/local`

This unpacks the Go files into the system directory /usr/local, which is a standard place for system-wide software.

```console
sudo tar -C /usr/local -xzf ./go1.25.0.linux-arm64.tar.gz
```

3. Add Go to your system PATH

This updates your .bashrc file so your shell can recognize the go command from anywhere.

```console
echo 'export PATH="$PATH:/usr/local/go/bin"' >> ~/.bashrc
```

4. Apply the PATH changes immediately

This reloads your .bashrc so you don’t need to log out and log back in for the changes to take effect.

```console
source ~/.bashrc
```

5. Verify Go installation

This checks if Go is installed correctly and shows the installed version.

```console
go version
```

You should see an output similar to: 

```output
go version go1.25.0 linux/arm64
```
6. Check Go environment settings

This displays Go’s environment variables (like GOROOT and GOPATH) to ensure they point to the correct installation.

```console
go env
```

You should see an output similar to: 

```output
AR='ar'
CC='gcc'
CGO_CFLAGS='-O2 -g'
CGO_CPPFLAGS=''
CGO_CXXFLAGS='-O2 -g'
CGO_ENABLED='0'
CGO_FFLAGS='-O2 -g'
CGO_LDFLAGS='-O2 -g'
CXX='g++'
GCCGO='gccgo'
GO111MODULE=''
GOARCH='arm64'
GOARM64='v8.0'
GOAUTH='netrc'
GOBIN=''
GOCACHE='/home/azureuser/.cache/go-build'
GOCACHEPROG=''
GODEBUG=''
GOENV='/home/azureuser/.config/go/env'
GOEXE=''
GOEXPERIMENT=''
GOFIPS140='off'
GOFLAGS=''
GOGCCFLAGS='-fPIC -fno-caret-diagnostics -Qunused-arguments -Wl,--no-gc-sections -fmessage-length=0 -ffile-prefix-map=/tmp/go-build3018594215=/tmp/go-build -gno-record-gcc-switches'
GOHOSTARCH='arm64'
GOHOSTOS='linux'
GOINSECURE=''
GOMOD='/dev/null'
GOMODCACHE='/home/azureuser/go/pkg/mod'
GONOPROXY=''
GONOSUMDB=''
GOOS='linux'
GOPATH='/home/azureuser/go'
GOPRIVATE=''
GOPROXY='https://proxy.golang.org,direct'
GOROOT='/usr/local/go'
GOSUMDB='sum.golang.org'
GOTELEMETRY='local'
GOTELEMETRYDIR='/home/azureuser/.config/go/telemetry'
GOTMPDIR=''
GOTOOLCHAIN='auto'
GOTOOLDIR='/usr/local/go/pkg/tool/linux_arm64'
GOVCS=''
GOVERSION='go1.25.0'
GOWORK=''
PKG_CONFIG='pkg-config'
```

Golang installation is complete. You can now proceed with the baseline testing.
