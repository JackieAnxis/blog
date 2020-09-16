---
title: 浅析JS模块化规范：CommonJS，AMD，CMD
date: 2016-10-17 11:07:00
author: FrontEnd
tags: ["FrontEnd", "JavaScript"]
---

## 模块化编程的进化过程

### 原始写法

这种做法的缺点很明显："污染"了全局变量，无法保证不与其他模块发生变量名冲突，而且模块成员之间看不出直接关系。

```js
function m1() {
  //...
}
function m2() {
  //...
}
```

### 对象写法

这样的写法会暴露所有模块成员，内部状态可以被外部改写。

```js
var module1 = new Object({
  _count: 0,
  m1: function () {
    //...
  },
  m2: function () {
    //...
  },
});
```

### 立即执行函数写法

使用"立即执行函数"（Immediately-Invoked Function Expression，IIFE），可以达到不暴露私有成员的目的。

```js
var module1 = (function () {
  var _count = 0;
  var m1 = function () {
    //...
  };
  var m2 = function () {
    //...
  };
  return {
    m1: m1,
    m2: m2,
  };
})();
```

JavaScript 模块的基本写法，之后对这种写法继续加工。

### 放大模式

继承其他模块，放大模式（augmentation）

```js
var module1 = (function (mod) {
  mod.m3 = function () {
    //...
  };
  return mod;
})(module1);
```

以上代码说明的是：module1 模块添加了一个新方法 m3()，然后返回新的 module1 模块

### 宽放大模式

有可能被继承的模块不存在，就会加载一个不存在的空对象，这时就要采用“款放大模式”

```js
var module1 = (function (mod) {
  //...
  return mod;
})(window.module1 || {});
```

### 输入全局变量

独立性是模块的重要特点，模块内部最好不与程序的其他部分直接交互。
为了在模块内部调用全局变量，必须显式地将其他变量输入模块。

```js
var module1 = (function ($, YAHOO) {
  //...
})(jQuery, YAHOO);
```

## 模块规范

### CommonJS

#### 编写模块

根据 CommonJS 规范，一个单独的文件就是一个模块，每一个模块都是一个单独的作用域，在一个文件定义的变量（还包括函数和类），都是私有的，对其他文件是不可见的。
通过`exports`或者`module.exports`来导出需要暴露的接口

#### 加载模块

`require()`函数用于加载模块：

```js
var math = require("math");
math.add(2, 3); // 5
```

**浏览器环境**：加载模块会导致浏览器阻塞，所以要采用“异步加载”方式，也就是 AMD 规范诞生的背景。所以 CommonJS 更适合服务器环境。

#### 评价

##### 优点

- 服务器端模块便于重用
- NPM 中已经有将近 20 万个可以使用模块包
- 简单并容易使用

##### 缺点

- 同步的模块加载方式不适合在浏览器环境中，同步意味着阻塞加载，浏览器资源是异步加载的
- 不能非阻塞的并行加载多个模块

### AMD

异步模块定义，AMD 是"Asynchronous Module Definition"的缩写.

#### 定义模块

```js
define(id?, dependencies?, factory);
```

- ID: 是一个字符串，表示模块标识。可以省略，则会定义一个匿名模块。
- dependencies: 是一个数组，成员是依赖模块的 id。可以省略，默认的依赖是：["require", "exports", "module"]
- factory：是一个回调函数，在依赖的模块加载成功后，会执行这个回调函数

一般写法：

```js
define("adder", ["math"], function (math) {
  return {
    addTen: function (x) {
      return math.add(x, 10);
    },
  };
});
```

默认依赖：

```js
define("adder", function (require, exports) {
  exports.addTen = function (x) {
    return x + 10;
  };
});
```

匿名模块：

```js
define(["math"], function (math) {
  return {
    addTen: function (x) {
      return math.add(x, 10);
    },
  };
});
```

兼容 CommonJS(匿名+默认依赖)：

```js
// module app/mime-client
define(function (require, exports, module) {
  var rest, mime, client;

  rest = require("rest");
  mime = require("rest/interceptor/mime");

  client = rest.chain(mime);

  module.exports = client;
});
```

#### exports 写法

1. 通过`exports`暴露接口

```js
define(function (require, exports) {
  exports.foo = "bar"; // 对外提供 foo 属性
  exports.doSomething = function () {}; // 对外提供 doSomething 方法
});
```

2. 通过`return`暴露接口

```js
define(function (require) {
  // 通过 return 直接提供接口
  return {
    foo: "bar",
    doSomething: function () {},
  };
});
```

3. 如果`return`是模块中唯一的代码，还可以写成这样：

```js
define({
  foo: "bar",
  doSomething: function () {},
});
```

4. 错误的写法：

```js
define(function (require, exports) {
  // 错误用法！！!
  exports = {
    foo: "bar",
    doSomething: function () {},
  };
});
```

5. 正确的写法是用`return`或者给`module.exports`赋值：

```js
define(function (require, exports, module) {
  // 正确写法
  module.exports = {
    foo: "bar",
    doSomething: function () {},
  };
});
```

**提示**：提示：`exports`仅仅是`module.exports`的一个引用。在`factory`内部给`exports`重新赋值时，并不会改变`module.exports`的值。因此给`exports`赋值是无效的，不能用来更改模块接口。

#### 加载模块

有两个参数，当模块加载完之后，才调用回调函数，第一个参数是一个数组，成员就是要加载的模块

```js
require([module], callback);
//example:
require(["math"], function (math) {
  math.add(2, 3);
});
```

目前，主要有两个 Javascript 库实现了 AMD 规范：require.js 和 curl.js。

#### requireJS

RequireJS 的基本思想为：通过一个函数来将所有所需要的或者说所依赖的模块实现装载进来，然后返回一个新的函数（模块）。
贴上[阮一峰的 requireJS 教程](http://www.ruanyifeng.com/blog/2012/11/require_js.html)

##### 引入 requireJS

- 普通方式：

```js
<script src="js/require.js"></script>
```

- 异步加载方式(`async`属性表明这个文件需要异步加载，避免网页失去响应。IE 不支持这个属性，只支持`defer`，所以把 defer 也写上)：

```js
<script src="js/require.js" defer async="true"></script>
```

- 最普遍的写法：

```js
<script data-main="scripts/main" src="scripts/require.js"></script>
```

`data-main`属性的作用是，指定网页程序的主模块。在上例中，就是 js 目录下面的`main.js`，这个文件会第一个被`require.js`加载。由于`require.js`默认的文件后缀名是 js，所以可以把`main.js`简写成 main。

##### main.js 的写法

require()函数接受两个参数。第一个参数是一个数组，表示所依赖的模块；第二个参数是一个回调函数，当前面指定的模块都加载成功后，它将被调用。加载的模块会以参数形式传入该函数，从而在回调函数内部就可以使用这些模块。

```js
// main.js
require(["moduleA", "moduleB", "moduleC"], function (
  moduleA,
  moduleB,
  moduleC
) {
  // some code here
});
```

代码含义：主模块的依赖模块是`['moduleA', 'moduleB', 'moduleC']`。默认情况下，`require.js`假定这三个模块与`main.js`在同一个目录，文件名分别为`moduleA.js`，`moduleB.js`和`moduleC.js`，然后自动加载。回调函数的参数只要位置对应即可，不一定要同名。

##### require.config() 自定义加载

require.config()就写在主模块（main.js）的头部，参数为一个对象。

```js
require.config({
  paths: {
    jquery: "lib/jquery.min",
    underscore: "lib/underscore.min",
    backbone: "lib/backbone.min",
  },
});
//或者
require.config({
  baseUrl: "js/lib",
  paths: {
    jquery: "jquery.min",
    underscore: "underscore.min",
    backbone: "backbone.min",
  },
});
```

##### 加载非 AMD 规范的模块

举例来说，underscore 和 backbone 这两个库，都没有采用 AMD 规范编写。如果要加载它们的话，必须先定义它们的特征。

```js
require.config({
  shim: {
    underscore: {
      exports: "_",
    },
    backbone: {
      deps: ["underscore", "jquery"],
      exports: "Backbone",
    },
  },
});
```

1. exports 值（输出的变量名），表明这个模块外部调用时的名称；
2. deps 数组，表明该模块的依赖性。

#### 评价

##### 优点

- 适合在浏览器环境中异步加载模块
- 可以并行加载多个模块

##### 缺点

- 提高了开发成本，代码的阅读和书写比较困难，模块定义方式的语义不顺畅
- 不符合通用的模块化思维方式，是一种妥协的实现

### CMD

`Common Module Definition`规范和`AMD`很相似，尽量保持简单，并与`CommonJS`和`Node.js`的`Modules`规范保持了很大的兼容性。在`Sea.js`中实现。
[**官方文档**：CMD 模块定义规范](https://github.com/seajs/seajs/issues/242)

#### 定义模块

和 AMD 标准定义模块的方式一致，但有一些不同，主要表现在增加了一些对象：

- `define.cmd`：一个空对象，可用来判定当前页面是否有 CMD 模块加载器

```js
if (typeof define === "function" && define.cmd) {
  // 有 Sea.js 等 CMD 模块加载器存在
}
```

- `require.async(id, callback?)`
  用来在模块内部异步加载模块，并在加载完成后执行指定回调。

```js
define(function (require, exports, module) {
  //异步加载一个模块，在加载完成时，执行回调
  require.async("./b", function (b) {
    b.doSomething();
  });
  // 异步加载多个模块，在加载完成时，执行回调
  require.async(["./c", "./d"], function (c, d) {
    c.doSomething();
    d.doSomething();
  });
});
```

- `require.resolve(id)`
  使用模块系统内部的路径解析机制来解析并返回模块路径。该函数不会加载模块，只返回解析后的绝对路径。

```js
define(function (require, exports) {
  console.log(require.resolve("./b"));
  // ==> http://example.com/path/to/b.js
});
```

#### 评价

与 RequireJS 的 AMD 规范相比，CMD 规范尽量保持简单，并与 CommonJS 和 Node.js 的 Modules 规范保持了很大的兼容性。
