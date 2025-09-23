---
title: Node.js baseline testing on Google Axion C4A Arm Virtual machine
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---


Since Node.js has been successfully installed on your GCP C4A Arm virtual machine, please follow these steps to make sure that it is running.

## Validate Node.js installation with a baseline test

### 1. Run a Simple REPL Test

Start the Node.js REPL:

```console
node
```
Inside, type:

```console
console.log("Hello from Node.js");
```
You should see an output similar to:

```output
Hello from Node.js
undefined
```

Allow HTTP Traffic in Firewall

```console
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload
```

### 2. Test a Basic HTTP Server

Create a file `app.js`:

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Baseline test successful!\n');
});

server.listen(3000, '0.0.0.0', () => {
  console.log('Server running at http://0.0.0.0:3000/');
});
```
Run the server:

```console
node app.js
```
You should see an output similar to:

```output
Server running at http://0.0.0.0:3000/
```
Open your browser or run from another terminal:

```console
curl http://localhost:3000
```

You should see an output similar to:

```output
Baseline test successful!
```

Also, you can access it from the browser with your VM's public IP. Run the following command to print your VM’s public URL, then open it in a browser:
```console
echo "http://$(curl -s ifconfig.me):3000/"
```

You should see the following message in your browser, confirming that your Node.js HTTP server is running successfully:

