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

`Node` 使用 `stream` 来处理 `HTTP` 的请求体，它注册了 `data` 和 `end` 两个事件

```js
var body = [];
request.on('data', function(chunk) {
  body.push(chunk);
}).on('end', function() {
  body = Buffer.concat(body).toString();
})
```

## Response对象

设置状态码 `statusCode` ，在 `Node` 并非必须，如果没有设置则默认都是200，但这不能代表所有的情况，所以，最佳实践就是手动设置状态码。

我们通过 `setHeader` 方法来设置 `response` 的头部信息

```js
response.setHeader('Content-Type': 'application/json');
response.setHeader('X-Powered-By', 'bacon');
```

`setHeader` 一次只能设置单个属性，如果想设置多个，可以使用 `writeHead` 方法。

```js
response.writeHead(200, {
  'Content-Length': Buffer.byteLength(body),
  'Content-Type': 'text/plain'
})
```

`response` 对象时一个 `writableStream` 实例，可以直接使用 `write` 方法写入，写入完成后再调用 `end` 方法发送到客户端

```js
response.write('<html>');
response.write('<body>');
response.write('<h1>Hello, World!</h1>');
response.write('</body>');
response.write('</html>');
response.end();
```

但这样写太麻烦，我们可以直接将返回的内容作为参数写到 `end` 方法中

```js
response.end('<html><body><h1>Hello, World!</h1></body></html>');
```

`response.end` 方法在每个 `HTTP` 请求的最后都会被调用。当客户端请求完成后，我们应该调用该方法来结束 `HTTP` 请求。

`end` 方法支持一个字符串或 `buffer` 作为参数，以指定请求最后返回的数据，如果定义了回调方法，那么会在 `end` 返回后调用。

```js
res.end('Hello Node', function() {
  console.log('http cycle end');
})
```

## 数据上传

在传统的Web开发中，最常用的HTTP请求只有 `get` 和 `post` 两种，`get` 请求的报文简单，只有请求行和请求头，`post` 还有请求体。

对于 `Node` 而言，可以通过 `req.method` 属性来判断请求方法的类型。

```js
var http = require('http');
var server = http.createServer(function(req, res) {
  if (req.method === 'get') {
    // todo..
  }

  if (req.method === 'post') {
    // todo...
  }
})
```

## HTTP客户端服务

HTTP模块除了能在服务端处理请求之外，还可以作为客户端向其他服务器发起请求。

```js
var http = require('http');
http.get('https://blockchain.info/ticker', function(res) {
  var statusCode = res.statusCode;
  if (statusCode === 200) {
    var result = '';
    res.on('data', function(data) {
      result += data;
    })
    res.on('end', function() {
      console.log(result.toString());
    })
    res.on('error', function() {
      console.log(e.message);
    })
  }
})
```