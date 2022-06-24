# socket.io
Socket.IO enables real-time bidirectional event-based communication. It consists of:

## Reliability
Connections are established even in the presence of:

- proxies and load balancers.
- personal firewall and antivirus software.
For this purpose, it relies on Engine.IO, which first establishes a long-polling connection, then tries to upgrade to better transports that are "tested" on the side, like WebSocket. Please see the Goals section for more information.

# Disconnection detection
- A heartbeat mechanism is implemented at the Engine.IO level, allowing both the server and the client to know when the other one is not responding anymore.

- That functionality is achieved with timers set on both the server and the client, with timeout values (the pingInterval and pingTimeout parameters) shared during the connection handshake. Those timers require any subsequent client calls to be directed to the same server, hence the sticky-session requirement when using multiples nodes.

```javascript 
io.on('connection', socket => {
  socket.emit('request', /* … */); // emit an event to the socket
  io.emit('broadcast', /* … */); // emit an event to all connected sockets
  socket.on('reply', () => { /* … */ }); // listen to the event
});
```

# How to use
```javascript 
const server = require('http').createServer();
const io = require('socket.io')(server);
io.on('connection', client => {
  client.on('event', data => { /* … */ });
  client.on('disconnect', () => { /* … */ });
});
server.listen(3000);

```


# In conjunction with Express
- Starting with 3.0, express applications have become request handler functions that you pass to http or http Server instances. You need to pass the Server to socket.io, and not the express application function. Also make sure to call .listen on the server, not the app.

