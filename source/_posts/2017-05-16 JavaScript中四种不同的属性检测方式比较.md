---
title: JavaScript中四种不同的属性检测方式比较
date: 2017-05-16 23:53:00
author: FrontEnd
tags: ["FrontEnd", "JavaScript"]
---

### JavaScript 中四种不同的属性检测方式比较

1. 用 in 方法
   ```JavaScript
   var o = {x:1};
   "x" in o; //true
   "y" in o; //false
   "toString" in o; //true，继承属性可以被检测到
   "toString" in Object.prototype; //true，不可枚举的属性可以被检测到
   ```
2. hasOwnProperty()方法
   ```JavaScript
   var o = {x:1};
   o.hasOwnProperty("x"); //true
   o.hasOwnProperty("y"); //false
   o.hasOwnProperty("toString"); //false，无法检测继承属性
   Object.prototype.hasOwnProperty("toString"); //true，不可枚举的属性可以被检测到
   ```
3. propertyIsEnumerable()方法
   ```JavaScript
   var o = Object.create({y:2});
   o.x = 1;
   o.propertyIsEnumerable("x"); //true，x是可枚举的属性
   o.propertyIsEnumerable("y"); //false，继承属性，不可枚举
   Object.prototype.propertyIsEnumerable("toString"); //false，不可枚举的属性无法被检测
   ```
4. !== undefined 方法
   ```JavaScript
   var o = {x : 1};
   o.x !== undefined; //true，o中有属性x
   o.toString !== undefined; //true，继承属性也可以被检测到
   ```
   这种方法的一个弱点是，无法区分不存在的属性和存在但值为 undefined 的值，如：
   ```JavaScript
   var o = {x : undefined};
   o.x !== undefined; //false
   "x" in o; //true
   ```
   注意这里用的是`"!=="`而不是`"!="`，因为`"!=="`可以区分`undefined`和`null`。
   但有时候不用区分`"null"`和`"undefined"`，只要判断一个属性不是`null`或`undefined`即可。
   ```JavaScript
   //如果o含有属性x，且x的值不是undefined或null，o.x乘以2
   if(o.x != null) o.x *= 2;
   ```
5. 总结
   继承属性是不可枚举的，所以能检测继承属性的，肯定也能检测到不可枚举属性。
   × 表示无法检测，√ 表示可以检测

| 检测方法             | 不可枚举属性？ | 继承属性？   |
| -------------------- | -------------- | ------------ |
| in                   | $\checkmark$   | $\checkmark$ |
| hasOwnProperty       | $\checkmark$   | $\times$     |
| propertyIsEnumerable | $\times$       | $\times$     |
| !==                  | $\checkmark$   | $\checkmark$ |
