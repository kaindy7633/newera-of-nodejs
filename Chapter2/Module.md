# Module

目前 `JavaScript` 有两种流行的模块化方案，分别是 `CommonJS` 和 `AMD`。

`CommonJS` 将每个文件看做一个模块，模块内部定义的变量都是私有的，无法被外部模块调用。但可以通过预定义方法（`expoets`、`require`）将其暴露出来。`CommonJS` 的应用实现就是 `Node`

`CommonJS` 有一个显著的特点就是模块加载是同步的。

`AMD` 就是 `Asynchronous Module Definition` 的缩写，意思就是异步模块定义，它采用异步方式加载模块。

## require

`CommonJS` 使用 `require` 关键字来加载模块

```js
// person.js
var person = {
  talk: function() {
    console.log('I am talking...');
  }，
  listen: function() {
    console.log('I am listening....');
  }
  // more functions ..
}
module.exports = person;
```

外部模块如果需要使用上面的 `person` 接口，使用 `require` 加载即可。

```js
var person = require('./perosn.js');
person.talk();
```

同时，我们也可以只引入 `person` 接口的一部分方法

```js
var talk = require('./person').talk;
talk();
```

在 `Node` 中，我们无需担心模块重复加载的问题，因为 `Node` 默认是从缓存中加载模块的，当一个模块首次被加载后，会在缓存中保留一个副本，后面如果再次加载同样的模块， `Node` 会从缓存中直接读取此模块的唯一的一个实例。

`require` 的缓存策略，上面说过，当 `Node` 第一次加载模块后，会在缓存中保留一份此模块的实例，这种缓存策略是基于**文件路**径来定位的，也就是说，如果有两个一模一样的文件，但其存放的路径不一样，那么 `Node` 也会保留两份模块的实例。

## 作用域

`Node` 中的作用域和浏览器中的有一些区别，它是以不同情况来区分的。

在控制台（`Node repl`）中，全局的 `this` 指向 `global` 对象。

而在文件脚本中，`this` 则指向了 `module.exports`

在 `Node` 中，有以下几种作用域类型：

- 全局作用域：如果一个变量没有使用 `var`、`let` 或 `const` 定义，那么它就属于全局作用域，可以通过 `global` 对象访问。
- 模块作用域：如果变量在文件中使用 `var`、`let` 或 `const` 定义，那么此时的 `this` 指向`module.exports`
- 函数作用域
- 块级作用域


