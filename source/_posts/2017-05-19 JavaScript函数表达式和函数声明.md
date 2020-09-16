---
title: JavaScript函数表达式和函数声明
date: 2017-05-19 00:53:00
author: FrontEnd
tags: ["FrontEnd", "JavaScript"]
---

## 函数表达式和函数声明

1. **函数声明**(Function Declaration):

   - 形如`function <function_name> () {}`，四个元素缺一不可
   - 不能够是表达式的其中一部分

2. **函数表达式**（Function Expression）:
   - 最常见的形式：`var func = function () {}`
   - 函数表达式一般是赋值表达式的一部分，不能单独存在。
   - 有时候，会给函数加上名字，像这样`var func = function f() {}`，但这实际上没什么用，跟用匿名函数赋值`var func = function() {}`是一样的。
   ```js
   var func = function f() {};
   f(); // Uncaught ReferenceError: f is not defined
   ```
   - 还有一种函数表达式，存在于**自执行函数**（self-invoking function）中。比如，下面这种形式，也是函数表达式。
   ```js
   (function sayHello() {
     alert("hello!");
   })();
   ```

### 两者之间的异同

- 同：这两个方法都能创建出一个函数，并且这个函数的名字都叫`foo`
- 异：
  1.  **函数声明**的方法，存在变量提升的情况，也就是会将这个函数声明提升到作用域最前面。于是，在函数声明前，就能调用这个函数。
      **函数表达式**的方法，其实也存在变量提升的情况，只不过先声明，但给这个变量赋值为`undefined`。在函数声明前调用这个函数就会报错。
  ```js
  (function () {
    a(); // a
    b(); // Uncaught TypeError: b is not a function
    function a() {
      console.log("a");
    } // 函数声明
    var b = function () {
      console.log("b");
    }; // 函数表达式
  })();
  ```
  2.  用**函数声明**的方法定义的函数，可以被放入执行环境的变量对象（Variable Object）中；而**函数表达式**定义的函数，不会被放入执行变量的
