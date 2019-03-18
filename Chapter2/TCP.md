# TCP服务

相对于HTTP服务，TCP的出场率并不高。

我们都知道 `Socket` ，它是对TCP协议的一种封装。`Socket` 并不是协议，而是一个编程接口，如果一种编程语言实现了 `Socket` 接口，那么就可以通过 `Socket` 接口预定义的方法来解析使用TCP协议传输的数据流。

`Node` 中有三种 `Socket`，分别对应实现TCP、UDP和Unix Socket。

Net模块和HTTP模块很相似，包含了 `Server` 类、`Socket` 类以及一些预定义方法。下面我们来创建一个TCP Server.

```js
var net = require('net');
var server = net.createServer(function(c) {
  console.log('client connected');
  c.on('end', function() {
    console.log('client disconnected');
  });
  c.write('hello\r\n');
  c.pipe(c);
});
server.on('error', function(err) {
  throw err;
});
server.listen(8124, function() {
  console.log('server bound');
})
```

下面我们写一个客户端来连接它：

```js
const net = require('net');
const client = net.connect({ port: 8124 }, function() {
  // 'connect' listener
  console.log('connected to server!');
  client.write('world!\r\n');
});
client.on('data', function(data) {
  console.log(data.toString());
  client.end();
});
client.on('end', function() {
  console.log('disconnected from server');
})
```

