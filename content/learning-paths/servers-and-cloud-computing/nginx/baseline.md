---
title: Nginx Baseline Testing 
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


### Baseline Testing with curl

Baseline testing with `curl` confirms Nginx is running and responding correctly with a `200 OK` status.

#### Run the following command to send a HEAD request to the local Nginx server:

```console
curl -I http://localhost/
```
The `curl -I http://localhost/` command sends a HEAD request to Nginx to check its **HTTP** response headers without downloading the page content.

You should see an output similar to:

```output
HTTP/1.1 200 OK
Server: nginx/1.25.4
Date: Wed, 30 Jul 2025 06:59:19 GMT
Content-Type: text/html
Content-Length: 615
Last-Modified: Tue, 29 Apr 2025 21:56:30 GMT
Connection: keep-alive
ETag: "68114b0e-267"
Accept-Ranges: bytes
```

Output summery:
- **HTTP/1.1 200 OK**: Nginx is responding successfully.
- **Server: nginx/1.25.4**: Confirms it's running Nginx.
- Confirms your web server is reachable on **localhost**.

This verifies the basic functionality of Nginx installation before proceeding to the benchmarking.
