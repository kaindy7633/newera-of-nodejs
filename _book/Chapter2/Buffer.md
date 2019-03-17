# Buffer

`Buffer` 是 `Node` 特有的数据类型，主要用来操作二进制数据。而在客户端浏览器，ES2015增加了 `ArrayBuffer` 类型用来操作二进制数据流。

在文件操作和网络操作中，如果不显式的声明编码格式，其返回数据默认的类型就是 `Buffer`。

```js
var fs = require('fs');
fs.readFile('foo.txt', function(err, results) {
  console.log(results);
  // <Buffer 48 65 6c 6c 6f 20 57 6f 72 6c 64> (Hello Node)
})
```

上面的代码中，打印的是十六进制的数据，由于二进制不便于阅读， `Buffer` 通常表现为十六进制的字符串。

## Buffer的构建与转换

可以直接使用 `Buffer` 类初始化一个 `Buffer` 对象，参数可以是由二进制数据组成的数组。

```js
var buffer = new Buffer([0x48, 0x65, 0x6c, 0x6c, 0x20, 0x4e, 0x6f, 0x64, 0x65]);
// Hello Node
```

同样可以使用构造函数，传入字符串来得到一个 `Buffer`

```js
var buffer = new Buffer('Hello Node');
console.log(buffer);
// <Buffer 48 65 6c 6c 6f 20 4e 6f 64 65>
```

目前在新版的 `Node` 中，已采用 `Buffer.from` 来初始换一个 `Buffer` 对象，上面的代码可以修改为如下：

```js
var buffer = Buffer.from([0x48, 0x65, 0x6c, 0x6c, 0x20, 0x4e, 0x6f, 0x64, 0x65]);
// Hello Node
var buffer = Buffer.from('Hello Node');
console.log(buffer);
// <Buffer 48 65 6c 6c 6f 20 4e 6f 64 65>
```

如果想要把 `Buffer` 转为字符串，可以使用 `toString` 方法

```js
buffer.toString([encoding], [start], [end])
// encoding: 目标编码格式
// start: 开始位置
// end： 结束位置
```

目前 `Buffer` 提供以下6种编码格式：

- `ASCII`
- `Base64`
- `Binary`
- `Hex`
- `UTF-8`
- `UTF-16LE/UCS-2`

`Buffer` 还提供了 `isEncoding` 方法来判断是否支持转换为目标编码格式。

```js
console.log(buffer.toString('utf-8', 0, 5));
// Hello
```

## Buffer的拼接

如果我们要处理的字符串中有中文字符，那么可能会出现回文问题，一个中文字符占3个字节，使用 `+=` 这样的方式拼接 `Buffer`时，由于这个过程包含一个隐式的编码转换，就会出现乱码问题。所以， `+=` 这样的方式已废弃，推荐使用 `push` 方法来拼接 `Buffer`

```js
var data = [];
// 现将Buffer放入数组里面
rs.on('data', function(chunk) {
  data.push(chunk);
});
// 结束后再进行编码转换
rs.on('end', function() {
  var buf = Buffer.concat(data);
  console.log(buf.toString());
})
```