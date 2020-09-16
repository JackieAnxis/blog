---
title: JavaScript闭包
date: 2017-07-27
author: FrontEnd
tags: ["FrontEnd", "JavaScript"]
---

## JavaScript 闭包

一般而言，因为存在作用域链的关系，JavaScript 函数内部可以访问函数外部的变量，而函数外部往往无法访问到函数内部的变量。

而 JavaScript 闭包的作用，就是通过改变作用域链的方式，使得函数外部可以访问到函数内部的作用域。详见下方的例子：

```js
var f = function () {
  var x = "x in f";
  var getX = function () {
    return x;
  };
  return getX;
};

var get = f();
console.log(get()); // 'x in f'
```

JavaScript 闭包，指的是一个函数内部的函数，通过将这个内部函数暴露给外界（如作为返回值返回、立即执行函数等），外界即可访问到这个函数内部的作用域。关于作用域的解释，可以详见我的另一篇博客：[JavaScript 执行环境和作用域](http://jackieanxis.coding.me/2017/07/24/JavaScript%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83%E5%92%8C%E4%BD%9C%E7%94%A8%E5%9F%9F/)。

![](http://jackie-image.oss-cn-hangzhou.aliyuncs.com/17-7-28/97746191.jpg)

上图中：

- 灰色部分表示了作用域链的构成，从最内部的`getX`到最外层的全局作用域`Global`。只能由作用域链顶端访问作用域链底端。
- 蓝色部分表示了一次赋值，此时将`f`内部的函数赋值给了作用域链底端的全局作用域，于是全局作用域链又能访问到`f`内部的变量了。

## 变量的生命周期

全局变量，会被当做 Global 变量的一个属性一直保存下来，于是它的生命周期是永久的。

局部变量，指的是函数内部的变量，一般情况下，当函数调用结束后，该变量就会被垃圾回收机制回收，再次调用该函数的时候，会再创建一个变量。

闭包的一个关键作用，就是能延长一个变量的生命周期。

```js
var f = function () {
  var count = 0;
  return function () {
    count++;
    return count;
  };
};

var count = f();
console.log(count()); // 1
console.log(count()); // 2
console.log(count()); // 3
```

事实上，变量依附于它存在的作用域，一旦该作用域从作用域链中弹出，这个作用域中的变量也随即销毁。

## 闭包经典问题

### 问题一：

需要给多个按钮绑定不同的事件：

```html
<button id="email"></button>
<button id="name"></button>
<button id="age"></button>
```

```js
var text = [
  { id: "email", text: "jackie@mail.com" },
  { id: "name", text: "Jackie" },
  { id: "age", text: "18" },
];
for (var i = 0; i < text.length; i++) {
  var item = text[i];
  document.getElementById(item.id).onclick = function () {
    alert(item.text);
  };
}
```

这时你会发现，点击任何一个 button，输出的都是 18，这和 JavaScript 的作用域机制有关，那么要怎么达到我们想要的效果，使得这几个回调函数不共享一个作用域：

```js
var text = [
  { id: "email", text: "jackie@mail.com" },
  { id: "name", text: "Jackie" },
  { id: "age", text: "18" },
];
var click = function (item) {
  return function () {
    alert(item.text);
  };
};
for (var i = 0; i < text.length; i++) {
  var item = text[i];
  document.getElementById(item.id).onclick = click(item);
}
```

当然，es6 标准中提出了更便捷、规范的解决方案，就是用`let`关键字：

```js
var text = [
  { id: "email", text: "jackie@mail.com" },
  { id: "name", text: "Jackie" },
  { id: "age", text: "18" },
];
for (var i = 0; i < text.length; i++) {
  let item = text[i];
  document.getElementById(item.id).onclick = function () {
    alert(item.text);
  };
}
```

### 问题二：

循环内的异步调用（本质和前者无区别）

```js
for (var i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i);
  }, i * 1000);
}
```

对应的正确解决方法，利用匿名函数存储变量 i

```js
for (var i = 0; i < 3; i++) {
  (function (i) {
    setTimeout(function () {
      console.log(i);
    }, i * 1000);
  })(i);
}
```

## 闭包的负面影响

闭包对脚本性能具有负面影响，包括处理速度和内存消耗。故而，在没有必要创建闭包的时候，就最好不要创建闭包。例子来自[MDN 闭包](https://developer.mozilla.org/cn/docs/Web/JavaScript/Closures)：

```js
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
  this.getName = function () {
    return this.name;
  };

  this.getMessage = function () {
    return this.message;
  };
}
```

改写如下：

```js
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
}
MyObject.prototype.getName = function () {
  return this.name;
};
MyObject.prototype.getMessage = function () {
  return this.message;
};
```
