---
title: JavaScript中的this关键字
date: 2017-07-25
author: FrontEnd
tags: ["FrontEnd", "JavaScript"]
---

## `this`的五种不同情形

### 默认情况

默认情况下，纯粹函数调用时，因为没有调用该方法的对象，故而此时的`this`是`undefined`，JavaScript 解释器会自动把这个对象指向全局对象 Global，在浏览器环境下，也即 window 对象。

```js
window.x = "Jackie";

function func() {
  console.log(this.x);
}

func(); // Jackie
```

阮一峰老师的博客中提到：

> 在严格模式（`"use strict"`）下，会禁止`this`指向全局对象，此时的`this`会是`undefined`。

事实上，[ES5 的规范](http://es5.github.io/#x10.4.3) 中修改了描述，仅当非严格模式下才会有 `this` 指向的转变。所以此时`this`无法改变指向，就会是`undefined`，但并不是说无法指向全局对象。

```javascript
"use strict";
function foo() {
  console.log(this);
}
foo.call(this); // Window
```

### 作为对象的方法调用

此时`this`指向调用这个方法的对象。

```js
var x = "Property of Window";

var obj = {};
obj.x = "Property of obj";
obj.f = function () {
  console.log(this.x);
};

obj.f(); // Property of obj

// 值得注意的情况
var f = obj.f;
f(); // Property of Window
```

### `call`、`apply`和`bind `的显式绑定

`call`、`apply`和`bind`都可以改变一个函数的`this`指向。

#### `call`和`apply`

`call`和`apply`会将它们的调用对象的`this`指向它们的第一个参数。

```js
function f() {
  console.log(this.x);
}

var x = "Property of Window";

var obj = {
  x: "Property of obj",
};

f.apply(obj); // "Property of obj"
```

当传入的第一个参数为 undefined，或者不传入参数时，在非严格模式下，自动会将`this`指向全局对象 Global，在浏览器里是`window`对象，严格模式下则会是`undefined`：

```js
function f() {
  console.log(this);
}

f.apply(); // window
f.apply(undefined); // window

function ff() {
  "use strict";
  console.log(this);
}
ff.apply(); // undefined
ff.apply(undefined); // undefined
```

`call`和`apply`没有本质区别。唯一的区别在于：

> `call()`方法接受的是**若干个参数的列表**，而`apply()`方法接受的是**一个包含多个参数的数组**。

#### `bind`

`bind`和前面两者也并未有什么本质的区别，只不过`bind`将第一个参数绑定当调用函数的`this`上，并将这个函数返回（不执行）。

```js
function f() {
  console.log(this.x);
}

var x = "Property of Window";

var obj = {
  x: "Property of obj",
};

var ff = f.bind(obj);
ff(); // "Property of obj"
```

### 构造函数

当一个函数被当做**构造函数**，用`new`关键字新建一个对象的时候，这个函数内部的`this`以及原型链上的`this`都会指向这个新建的对象。

```js
function Jackie(para) {
  this.para = para;
  console.log(this);
}
Jackie.prototype.log = function () {
  console.log(this);
};

Jackie("hehe"); // Window
var p = new Jackie("haha"); // Jackie {para: "haha"}
p.log(); // Jackie {para: "haha"}
```

### 其他值得注意的绑定

#### 放在超时代码里

JavaScript 中超时调用的代码，函数中 this 的值会指向 window 对象，在严格模式下也一样。因为超时调用的代码都会有一个隐式绑定：`setTimeout(f, time) == setTimeout(f.bind(window), time)`。

```js
"use stric";
var x = "Property of Window";

var obj = {};
obj.x = "Property of obj";
obj.ff = function () {
  setTimeout(function () {
    console.log(this.x);
  }, 100);
};

obj.ff(); // Property of Window

// 可以这么解决问题
obj.fff = function () {
  var that = this;
  setTimeout(function () {
    console.log(that.x);
  }, 100);
};
obj.fff(); // Property of obj
```

#### 事件监听函数中的 this

事件监听函数中的`this`指向监听对象。

```js
var one = document.getElementById("one");
one.onclick = function () {
  console.log(this);
};

one.click(); // <div id="one"></div>
```

#### 箭头函数

箭头函数中`this`的指向，在函数定义时即绑定完毕，且后续无法更改。（即，会跟它的上级作用域的`this`指针保持一致）

```json
var obj = {
  x: 1
}

var f1 = () => {
  console.log(this)
}
f1.apply(obj) // Window

var f2 = function () {
  var f3 = () => {
    console.log(this)
  }
  return f3
}

var f4 = f2.apply(obj)
f4() // Object {x: 1}
```

一个更神奇的例子，超时调用的代码在定义时，绑定了`this`的指向。

```js
function foo() {
  setTimeout(() => {
    console.log("id:", this.id);
  }, 100);
}

var id = 21;

foo.call({ id: 42 }); // id: 42
```

## 绑定的优先级

```js
var obj = { x: 0, name: "obj" };
var robj = { x: -1, name: "robj" };
var factory = function (x) {
  this.x = x;
  console.log(this);
};

var factoryBind = factory.bind(obj);
robj.factory = factoryBind;
robj.factory(2); // Object {x: 2, name: "obj"}，作为方法的绑定的优先级低于bind的显式绑定

factoryBind.call(robj, 3); // Object {x: 3, name: "obj"}，call的优先级低于bind
console.log(robj); // Object {x: -1, name: "robj", factory: function}，未对robj进行修改
console.log(obj); // Object {x: 3, name: "obj"}，修改的是obj，因为this指针指向未变化

var p = new factoryBind(4); // factory {x: 4}
console.log(p); // factory {x: 4}
console.log(obj); // Object {x: 3, name: "obj"}，构造函数绑定的优先级高于bind的显式绑定
```

可以见得，优先级从高到低：

1. `new`，构造绑定
2. `bind`，显式绑定
3. `call`/`apply`，显示绑定
4. 作为方法绑定
5. 默认绑定
