# File System

`File System` 模块为 `Node` 提供了文件读写的能力，它借助了底层 `libuv` 的 `C++` API实现的。`File System` 包含了数十个用于文件操作的API，大多都提供了同步和异步两个版本。

## readFile

```js
fs.readFile(file [, options], callback);
```

该方法异步读取文件中的内容

```js
fs.readFile('foo.txt', function(err, data) {
  if (err) throw err;
  console.log(data);
})
```

`readFile` 将一个文件的所有内容都读取到内存中，适用于小文件，对于过大的文件，则建议使用 `stream` 来处理。`readFile` 是异步读取文件内容，而 `readFileSync` 则是直接返回文件数据内容。

```js
var fs = require('fs');
var data = fs.readFileSycn('foo.txt', { encoding: 'utf-8' }); 
// 不指定encoding，会返回buffer，需要调用toString方法转换一次
console.log(data);
```

## writeFile

```js
fs.writeFile(file, data[, opotions], callback);
```

如果要写入内容的文件不存在，默认则会创建此文件

```js
fs.writeFile('foo.txt', 'Hello Node', { flag: 'a', encoding: 'utf-8' }, function(err) {
  if (err) {
    console.log(err);
    return;
  }
  console.log('success');
})
```

## stat

```js
fs.stat(path, callback);
```

该方法通常用来获取文件状态，比如在执行 `open`、`read` 等方法前先检查文件是否存在

```js
var fs = require('fs');
fs.stat('foo.txt', function(err, result) {
  if (err) throw err;
  console.log(result)
})
```

在 `File System` 模块提供的API中，还有一个 `fstat` ，它与 `stat` 在功能上是等价的，唯一不同的是，`fstat` 方法的第一个参数是文件描述符，它通常与 `open` 方法配合使用

```js
fs.open('foo.txt', function(err, fd) {
  if (err) throw new Error(err);
  console.log(fd);

  fs.fstat(fd, function(err, result) {
    if (err) throw new Error(err);
    console.log(result);
  })
})
```

在日常应用中，我们经常使用 `readdir` 和 `stat` 两个方法来获取目录下的所有文件名，`fs.readdir` 方法获取当前目录下所有文件或子目录，而 `fs.stat` 用于判断每条记录是文件还是文件夹，如果是文件夹，则递归调用自己。

```js
var fs = require('fs');
function getAllFileFromPath(path) {
  fs.readdir(path, function(err, res) {
    if (err) throw new Error(err);
    for (let subPath of res) {
      // 使用同步方法
      let statObj = fs.statSync(path + '/' + subPath);
      if (statObj.isDirectory()) {  // 判断是否为目录
        console.log('Dir: ', subPath);
        getAllFileFromPath(path + '/' + subPath);
      } else {
        console.log('File: ', subPath);
      }
    }
  })
}

getAllFileFromPath(__dirname);
```
