```console
sudo wget https://nodejs.org/dist/latest/node-v24.8.0-linux-arm64.tar.gz
sudo tar -xvf node-v24.8.0-linux-arm64.tar.gz
sudo mv node-v24.8.0-linux-arm64 node-v24.8.0
sudo ln -s /usr/local/node-v24.8.0 /usr/local/node
echo 'export PATH=$HOME/node-v24.8.0/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
node -v
npm -v
```
2. Run a Simple REPL Test

Start the Node.js REPL:

node


Inside, type:

console.log("Hello from Node.js");
gcpuser@lpprojectnewsusearm64:~> node
Welcome to Node.js v24.8.0.
Type ".help" for more information.
> console.log("Hello from Node.js");
Hello from Node.js
undefined


🧪 Node.js Baseline Testing Guide
1. Verify Node.js and npm

Check installed versions:

node -v
npm -v


Expected:

v24.8.0
10.x.x   (or higher)

2. Run a Simple REPL Test

Start the Node.js REPL:

node


Inside, type:

console.log("Hello from Node.js");


Exit with:

.exit

3. Create a Simple Script

Create a file hello.js:

console.log("Node.js is working correctly!");


Run:

node hello.js


Expected output:

Node.js is working correctly!

4. Test a Basic HTTP Server

Create app.js:

const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Baseline test successful!\n');
});

server.listen(3000, () => {
  console.log('Server running at http://localhost:3000/');
});


Run:

node app.js


You should see:

Server running at http://localhost:3000/

5. Verify with curl

From another terminal:

curl http://localhost:3000


Expected:

Baseline test successful!


gcpuser@lpprojectnewsusearm64:~> autocannon -c 100 -d 30 -p 10 http://127.0.0.1:3000
Running 30s test @ http://127.0.0.1:3000
100 connections with 10 pipelining factor


┌─────────┬───────┬───────┬───────┬───────┬──────────┬─────────┬────────┐
│ Stat    │ 2.5%  │ 50%   │ 97.5% │ 99%   │ Avg      │ Stdev   │ Max    │
├─────────┼───────┼───────┼───────┼───────┼──────────┼─────────┼────────┤
│ Latency │ 19 ms │ 21 ms │ 43 ms │ 43 ms │ 23.79 ms │ 9.45 ms │ 662 ms │
└─────────┴───────┴───────┴───────┴───────┴──────────┴─────────┴────────┘
┌───────────┬─────────┬─────────┬────────┬─────────┬──────────┬─────────┬─────────┐
│ Stat      │ 1%      │ 2.5%    │ 50%    │ 97.5%   │ Avg      │ Stdev   │ Min     │
├───────────┼─────────┼─────────┼────────┼─────────┼──────────┼─────────┼─────────┤
│ Req/Sec   │ 38,463  │ 38,463  │ 41,247 │ 41,375  │ 41,113.6 │ 511.68  │ 38,438  │
├───────────┼─────────┼─────────┼────────┼─────────┼──────────┼─────────┼─────────┤
│ Bytes/Sec │ 7.46 MB │ 7.46 MB │ 8 MB   │ 8.03 MB │ 7.98 MB  │ 99.6 kB │ 7.46 MB │
└───────────┴─────────┴─────────┴────────┴─────────┴──────────┴─────────┴─────────┘

Req/Bytes counts sampled once per second.
# of samples: 30

1234k requests in 30.02s, 239 MB read
