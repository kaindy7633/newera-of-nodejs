# Stream

`Stream` 模块为Node操作流式数据提供了支持。要使用 `Stream`模块，需要增加引用。

```js
var stream = require('stream');
```

## 分类

在Node中，有四种基础的 `stream` 类型：

- `Readable`：可读流（`fs.createReadStream()`）
- `Writable`：可写流（`fs.createWriteStream()`）
- `Duplex`：既可读，又可写(`net.Socket`)
- `Transform`：操作写入的数据，然后读取结果。

