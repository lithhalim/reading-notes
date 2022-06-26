# Get started

In this guide we’ll create a basic chat application. It requires almost no basic prior knowledge of Node.JS or Socket.IO, so it’s ideal for users of all knowledge levels.

# Introduction
Writing a chat application with popular web applications stacks like LAMP (PHP) has normally been very hard. It involves polling the server for changes, keeping track of timestamps, and it’s a lot slower than it should be.

Sockets have traditionally been the solution around which most real-time chat systems are architected, providing a bi-directional communication channel between a client and a server.

This means that the server can push messages to clients. Whenever you write a chat message, the idea is that the server will get it and push it to all other connected clients.

```javascript
{
  "name": "socket-chat-example",
  "version": "0.0.1",
  "description": "my first socket.io app",
  "dependencies": {}
}

```

Now, in order to easily populate the dependencies property with the things we need, we’ll use npm install:

npm install express@4
Once it's installed we can create an index.js file that will set up our application.




```javascript
const express = require('express');
const app = express();
const http = require('http');
const server = http.createServer(app);

app.get('/', (req, res) => {
  res.send('<h1>Hello world</h1>');
});

server.listen(3000, () => {
  console.log('listening on *:3000');
});

```


Integrating Socket.IO
Socket.IO is composed of two parts:

- A server that integrates with (or mounts on) the Node.JS HTTP Server socket.io
- A client library that loads on the browser side socket.io-client
During development, socket.io serves the client automatically for us, as we’ll see, so for now we only have to install one module:

npm install socket.io
That will install the module and add the dependency to package.json. Now let’s edit index.js to add it:

