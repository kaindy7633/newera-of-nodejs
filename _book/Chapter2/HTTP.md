# HTTP服务

`HTTP` 模块是 `Node` 的核心模块，主要提供了一系列用于网络传输的API

下面是在 `HTTP` 模块中定义的顶级类、属性以及方法：

- Class: `http.Agent`
- Class: `http.ClientRequest`
- Class: `http.Server`
- Class: `http.ServerResponse`
- Class: `http.IncomingMessage`
- `http.METHODS`
- `http.STATUS_CODES`
- `http.createClient([port] [, host])`
- `http.createServer([requiestListener])`
- `http.get(options[, callback])`
- `http.globalAgent`
- `http.request(options[, callback])`

## 创建HTTP服务器

通常我们使用 `createServer` 方法来创建 `HTTP` 服务器

```js
var http = require('http');
var server = http.createServer(function(req, res) {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello Node!');
});

server.listen(3000);
```

上面的代码中，`createServer` 方法返回一个 `http.server` 类的实例，它包含一个匿名的回调函数，该函数有 `req` 和 `res` 两个参数，分别是 `InComingMessage` 和 `ServerResponse` 的实例，代表 `request` 和 `response` 对象，服务创建完成后， `Node` 进程开始循环监听3000端口。

当客户端访问该服务时，会触发 `connection` 和 `request` 事件，示例如下：

```js
var http = require('http');
var server = http.createServer(function(req, res) {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello Node');
});


server.on('connection', function(req, res) {
  console.log('connected');
});

server.on('request', function(req, res) {
  console.log('request');
});

server.listen(3000);
// connected
// request
// request
```

下面我们来创建一个简单的文件服务器

```js
var http = require('http');
var fs = require('fs');
var server = http.createServer(function(req, res) {
  if (req.url === '/') {
    // 访问的是根目录，显式文件列表
    var fileList = fs.readFileSync('./');
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    // 将数组转换为字符串后打印
    res.end(fileList.toString());
  } else {
    var path = req.url;
    fs.readFile(`.${path}`, function (err, data) {
      if (err) {
        res.end('Internal Error');
        throw new Error(err);
      }
      res.writeHead(200, { 'Content-Type': 'text/plain' });
      res.end(data);
    });
  }
});

server.listen(9527);
console.log('server is running at 9527...');

// 处理异常
process.on('uncaughtException', function() {
  console.log('got error');
})
```

## 处理http请求

当处理 `http` 请求时，最先做的就是获取请求的 `URL`、`method` 等信息。`Node` 将相关信息都封装在 `request` 对象中，它是 `IncomingMessage` 的实例。

```js
var method = req.method;
var url = req.url;
```

`method` 的值通常是 `get`、 `post`、`put`、`delete` 和 `update`.

`header` 是一个JSON对象，可以通过属性名进行单独索引

```js
var headers = req.headers;
var userAgent = headers['user-agent'];
```

