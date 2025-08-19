---
title: Golang Baseline Testing 
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


### Baseline testing of Go Web Page on Azure Arm64
This guide demonstrates how to test your Go installation on Azure Arm64 by creating and running a simple Go web server that serves a styled HTML page.

1. Create Project Directory

First, create a new folder called goweb to hold your project and move inside it:

```console
mkdir goweb && cd goweb
```
This makes a directory named goweb and then changes into it.

2. Create HTML Page with Bootstrap Styling

Next, create a file named `index.html` using the nano editor:

```console
nano index.html
```

Paste the following HTML code inside. This builds a simple, styled web page with a header, a welcome message, and a button using Bootstrap. Save the file with CTRL+O and exit with CTRL+X.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Azure Arm64 Go Web</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
    <nav class="navbar navbar-dark bg-primary">
        <div class="container-fluid">
            <span class="navbar-brand mb-0 h1">Go Web on Azure ARM64</span>
        </div>
    </nav>
    <div class="container mt-5">
        <div class="card shadow-lg p-4">
            <h1 class="text-center text-primary">Hello from Golang!</h1>
            <p class="lead text-center">This page is served directly from a Go web server running on Azure ARM64.</p>
            <div class="text-center">
                <a href="https://go.dev" target="_blank" class="btn btn-success">Learn More about Go</a>
            </div>
        </div>
    </div>
</body>
</html>
```
3. Create Go Web Server

Now create the Go program that will serve this web page:

```console
nano main.go
```
Paste the code below. This sets up a very basic web server that serves files from the current folder, including the **index.html** you just created. When it runs, it will print a message showing the server address.

```go
package main

import (
    "fmt"
    "log"
    "net/http"
)

func main() {
    fs := http.FileServer(http.Dir("."))
    http.Handle("/", fs)

    fmt.Println("Server running on http://localhost:8080")
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

4. Run the Web Server

Run your Go program with:

```console
go run main.go
```

This compiles and immediately starts the server. If successful, you’ll see the message:

```output
2025/08/19 04:35:06 Server running on http://0.0.0.0:80
```
5. Open in Browser

Finally, open your browser and go to:

http://< <Public-IP> >:8080

You should see the Golang web page confirming a successful installation of Golang.

![golang](./go-web.png)
Replace <**Public-IP**> with your Azure virtual machine’s actual public IP address. When you visit this link, you should see the styled HTML page being served directly by your Go application.
