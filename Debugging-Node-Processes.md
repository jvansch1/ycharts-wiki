## Debugging Node Memory Issues

We can use `node-inspector` to inspect the heap. See [this blog post](https://www.toptal.com/nodejs/debugging-memory-leaks-node-js-applications)
for a more general overview of debugging memory leaks in a node app. This page will have more detail on how to use it specifically with YCharts'
`chart_image_generator`

### Install node-inspector

```bash
sudo npm install -g node-inspector
```

### Start debugging mode

```bash
# Find pid of slave node process
$ ps -ef
...
root     26700     1  0 Mar03 ?        00:00:00 /usr/local/bin/node /sites/chart_image_generator/ycharts_server.js
root     26703 26700  0 Mar03 ?        00:05:09 /usr/local/bin/node /sites/chart_image_generator/ycharts_server.js
root     26704 26700  0 Mar03 ?        00:05:41 /usr/local/bin/node /sites/chart_image_generator/ycharts_server.js
root     26706 26700  0 Mar03 ?        00:05:13 /usr/local/bin/node /sites/chart_image_generator/ycharts_server.js
root     26708 26700  0 Mar03 ?        00:06:13 /usr/local/bin/node /sites/chart_image_generator/ycharts_server.js

# Send SIGUSR1 signal
kill -SIGUSR1 26703
```

### Start node-inspector

```bash
# Our node processes run as root, so sudo is necessary
sudo node-inspector
```

### SSH Tunnel localhost to node server

```bash
# Get the proxy command from the ssh config
ssh -L 8080:localhost:8080 -o ProxyCommand='ssh ec2-user@52.4.31.32 -W %h:%p' ubuntu@[node_ip]
```

### Debug it!

Go to http://localhost:8080 and you are in something like the Chrome dev tools and you can take heap snapshots and edit the source code and view the console
of the live node process! Hopefully, there is something useful there!
