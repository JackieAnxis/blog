---
title: JavaScript执行环境和作用域
date: 2017-07-24 21:21:21
author: FrontEnd
tags: ["FrontEnd", "JavaScript"]
---

# JavaScript 执行环境和作用域

## 重要概念

- **执行环境**（excution context，有时候也称之为“环境”）

- **环境栈**：当执行流进入一个函数时，函数的环境就会被推入一个环境栈中。执行之后，再从环境栈中弹出。

- **作用域链**(scope chain):保证对执行环境有权访问的所有变量和函数的有序访问。作用域链的前端，始终都是当前执行的代码所在环境的变量对象。

- 请谨记：

  > JavaScript 中的函数运行在它们被定义的作用域里,而不是它们被执行的作用域里。
  >
  > ——《JavaScript 权威指南》

## 变量对象 和 活动对象

### 变量对象

**变量对象**（variable object，每个执行环境都有一个与之关联的变量对象），变量对象保存了以下三个内容：

1. 形参（如果执行环境是一个函数的话）
2. `var`声明的**变量**。

   - 用`let`和`const`声明变量只存在于块级作用域内，无法放入执行环境栈的变量对象中。
   - 直接声明的变量，比如`a=10`，其实不能称之为变量，它只是全局对象的一个属性，也无法保存进变量对象中。

   ```js
   console.log(a); // undefined
   console.log(b); // Uncaught ReferenceError: b is not defined
   b = 10;
   var a = 20;
   ```

3. 函数声明，不包括函数表达式。**函数声明**和**函数表达式**的区别，在我的另一篇博客里有说明：[JavaScript 函数表达式和函数声明](http://jackieanxis.coding.me/2017/05/19/JavaScript%E5%87%BD%E6%95%B0%E8%A1%A8%E8%BE%BE%E5%BC%8F%E5%92%8C%E5%87%BD%E6%95%B0%E5%A3%B0%E6%98%8E/)。

### 活动对象

**活动对象**（activation object），如果所在环境是函数，那么就会把这个函数的**活动对象**作为变量对象(在函数中，变量对象==活动对象)。它一开始只包含`arguments`对象。一般而言，函数执行过程，可以分成两步：1.进入执行环境；2.执行代码。
比如，下面这个`test`函数：

```JavaScript
function test(a, b) {
	var c = 10;
	function d() {}
	var e = function _e() {};
	(function f() {});
	g = 10;
}

test(10);
```

在执行`test(10)`的时候，分成了两步：

1. 进入执行环境。
   此时，会用`arguments`对象初始化活动对象（AO, activation object）。并且，会把形参、`var`声明的变量和函数声明放入活动对象 AO 中。
   ```JS
   AO: {
     arguments: {
         callee: test,
         length: 1,
         0: 10
     },
     a: 10,
     b: undefined,
     c: undefined,
     d: <reference to FunctionDeclaration "d">,
     e: undefined
   }
   ```
2. 执行代码。AO 会变成：
   ```JS
   AO: {
     arguments: {
         callee: test,
         length: 1,
         0: 10
     },
     a: 10,
     b: undefined,
     c: 10,
     d: <reference to FunctionDeclaration "d">,
     e: <reference to FunctionDeclaration "_e">
   }
   ```

## 作用域链

**作用域链**与一个执行上下文相关，是内部上下文所有变量对象（包括父变量对象）的列表，用于变量查询。

**作用域链的用途，是保证对执行环境有权访问的所有变量和函数的有序访问。**

**全局执行环境的变量对象始终都是作用域链中的最后一个对象。**

### 函数生命周期

#### 函数创建（定义）

- 在一个函数被**定义**的时候, 会将它**定义时刻**的 scope chain 链接到这个函数对象的`[[Scopes]]`属性，函数可以通过这个属性来访问更高层的作用域。

- `[[Scopes]]`是所有父变量对象的层级链，处于当前函数上下文之上，在函数创建时存于其中。

- `[[Scopes]]`在函数创建时被存储－－静态（不变的），永远永远，直至函数销毁。

- `[[Scopes]]`存储的是定义时刻的作用域链，是函数本身**所依赖的变量对象**+其所在环境的`[[Scopes]]`组成的。值得注意的是，`[[Scopes]]`不会存储函数本身不依赖的变量对象（也就是不存在冗余），例子如下：

  ```js
  var func = function () {
    var x = 0;
    var y = 1;
    var f = function () {
      console.log(x);
    };
    return f;
  };
  func();
  ```

  `f`函数的`[[Scopes]]`属性：

  ```js
  [[Scopes]] = [
    {
      x: 0,
    },
    Global(Window),
  ];
  ```

  故而，`[[Scopes]]`属性和作用域链有微小的区别，但是使用起来可以当做一致，本文后面也将它们视作一致。

- 与作用域链对比，`[[Scopes]]`是函数的一个属性而不是上下文。

- 一个简单的例子：
  ```js
  var a = 0;
  var f = function () {
    console.log(a);
  };
  var f1 = function () {
    var a = 1;
    f();
  };
  f1(); // 0
  ```

#### 函数激活（执行）

```
scope chain = VO/AO + [[Scopes]]
```

- 一个详细的例子（转载自：[Javascript 作用域原理@Laruence](http://www.laruence.com/2009/05/28/863.html)）

  ```js
  function factory() {
    var name = "laruence";
    var intro = function () {
      alert("I am " + name);
    };
    return intro;
  }

  function app(para) {
    var name = para;
    var func = factory();
    func();
  }

  app("eve");
  ```

  1. 调用`app`，刚进入执行上下文时，此时`app`的作用域链（scope chain）应该是活动对象（AO）加上其`[[Scopes]]`属性

     ```js
     [[Scopes]] = [
       Global(Window)
     ]

     scope chain = [{
       para: 'eve',
       name: undefined,
       func: undefined,
       arguments: [1]
     }, // AO
     Global(Window)]
     ```

  2. 调用`factory`，刚进入执行上下文时，此时`factory`的作用域链：`factory`的活动对象+其`[[Scopes]]`属性

     ```js
     [[Scopes]] = [
       Global(Window)
     ]

     scope chain = [
     {
         name: undefined,
     	intro: undefined,
       	arguments: []
     }, // AO
     Global(Window)]
     ```

     此时的作用域链中，不包含 app 的活动对象，因为函数内部只能通过`[[Scopes]]`属性访问上层的作用域。

  3. 定义`intro`函数时候，会将当前的作用域链写入`intro`函数的`[[Scopes]]`属性

     ```js
     [[Scopes]] = [{
         name: 'laruence',
       	intro: function(),
       	arguments: []
     },
     Global(Window)]
     ```

  4. 从`factory`函数返回以后，在`app`体内调用`intro`的时候，进入`intro`的执行上下文时，`scope chain`应该是这样的：

     ```js
     scope chain = [{
     },   // AO
     // 下面是[[Scopes]]属性
     {
       name: 'laruence',
       intro: function(),
       arguments: []
     }, Global(Window)]
     ```

  5. 故而，输出是：`I am laruence`

## `eval`函数

代码`eval`的上下文与当前的调用上下文（calling context）拥有同样的作用域链。

唯一的小小的区别是，因为`eval`函数创建的函数，在定义阶段，无法确定函数内部依赖活动对象中的哪些属性，故而会把整个活动对象都写入`[[Scopes]]`属性中。

```js
function factory() {
  var name = "laruence";
  eval("var intro = function () { console.log('I am ' + name); }");
  return intro;
}
factory();
```

`intro`函数的`[[Scopes]]`属性：

```js
[[Scopes]] = [{
  arguments: [],
  intro: function,
  name: 'laruence'
}, Global(Window)]
```

在严格模式下，`eval`语句本身就是一个作用域，不再能够生成全局变量了，它所生成的变量只能用于`eval`内部。

## `with`语句

- [MDN 上关于 with 的定义](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/with)

- [Javascript 中的 with 关键字](http://luopq.com/2016/02/14/js-with-keyword/)

- 总而言之：`with`语句会将传入的对象，添加到作用域的最前端。

  ```js
  var x = 10,
    y = 10;

  with ({ x: 20 }) {
    var x = 30,
      y = 30;
    alert(x); // 30
    alert(y); // 30
  }

  alert(x); // 10
  alert(y); // 30
  ```

  1. x = 10, y = 10;
  2. 对象{x:20}添加到作用域的前端;
  3. 在 with 内部，遇到了 var 声明，当然什么也没创建，因为在进入上下文时，所有变量已被解析添加;
  4. 在第二步中，仅修改变量“x”，实际上对象中的“x”现在被解析，并添加到作用域链的最前端，“x”为 20，变为 30;
  5. 同样也有变量对象“y”的修改，被解析后其值也相应的由 10 变为 30;
  6. 此外，在 with 声明完成后，它的特定对象从作用域链中移除（已改变的变量“x”－－30 也从那个对象中移除），即作用域链的结构恢复到 with 得到加强以前的状态。
  7. 在最后两个 alert 中，当前变量对象的“x”保持同一，“y”的值现在等于 30，在 with 声明运行中已发生改变。

- `catch`也具有相似的效应。
