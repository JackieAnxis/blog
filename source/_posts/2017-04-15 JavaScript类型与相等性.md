---
title: JavaScript类型转换和相等性
date: 2017-04-15 14:18:00
author: FrontEnd
tags: ["FrontEnd", "JavaScript"]
---

## 类型转换总结列表

| 值                                    | 字符串                                         | 数字     | 布尔值 | 对象                 |
| :------------------------------------ | :--------------------------------------------- | :------- | :----- | :------------------- |
| undefined                             | "undefined"                                    | NaN      | false  | throwTypeError       |
| null                                  | "null"                                         | 0        | false  | throwTypeError       |
| true                                  | "true"                                         | 1        |        | newBoolean(true)     |
| false                                 | "false"                                        | 0        |        | newBoolean(false)    |
| ""（空字符串）                        |                                                | 0        | false  | newString("")        |
| "1.2"（非空，数字，允许首尾带有空格） |                                                | 1.2      | true   | newString("1.2")     |
| "one"（非空，非数字）                 |                                                | NaN      | true   | newString("one")     |
| 0                                     | "0"                                            |          | false  | newNumber(0)         |
| -0                                    | "0"                                            |          | false  | newNumber(-0)        |
| NaN                                   | "NaN"                                          |          | false  | newNumber(NaN)       |
| Infinity                              | "Infinity"                                     |          | true   | newNumber(Infinity)  |
| -Infinity                             | "-Infinity"                                    |          | true   | newNumber(-Infinity) |
| 1（非无穷大，非零）                   | "1"                                            |          | true   | newNumber(1)         |
| {}（任意对象）                        | 详见下方                                       | 详见下方 | true   |                      |
| []（任意数组）                        | ""                                             | 0        | true   |                      |
| [9]（1 个数字元素）                   | "9"                                            | 9        | true   |                      |
| ['a']（其他数组）                     | 使用`join()`方法（默认用逗号连接）             | NaN      | true   |                      |
| function(){}（任意函数）              | 调用`toString()`方法，通常是转换成源代码字符串 | NaN      | true   |                      |

## 对象如何转换成字符串或数字

1. `toString()`方法：
   - 调用`toString()`方法，一般是返回`"[object object]"`：
   ```js
   ({ x: 1, y: 2 }.toString()); // => "[object object]"
   ```
   - **数组**是调用`join()`方法：
   ```js
   [1, 2, 3].toString(); // => "1,2,3"
   ```
   - **函数**则是转换成源代码字符串
   ```js
   (function (x) {
     f(x);
   }.toString()); // => "function(x){ f(x); }"
   ```
   - **Date 类型**会返回一个可读的日期和时间字符串:
   ```js
   new Date(2010, 0, 1).toString(); // => "Fri Jan 01 2010 00:00:00 GMT+0800 (China Standard Time)"
   ```
   - **RegExp 类**则是转换成正则表达式字符串
   ```js
   /\d+/g.toString(); // => "/\\d+/g"
   ```
2. `valueOf()`方法：

- 如果对象存在任意原始值，默认将对象转换成它的原始值（比如各种原始对象的包装对象）
- 一般，都是简单的返回对象本身
- 特殊的是：**Date**类型，日期类的`valueOf()`会返回一个内部表示：从 1970 年 1 月 1 日以来的毫秒数：

```js
var d = new Date(2010, 0, 1);
d.valueOf(); // => 1262275200000
```

3. 对象到字符串的转换步骤：
   ![](http://jackie-image.oss-cn-hangzhou.aliyuncs.com/17-4-18/49990859-file_1492446304461_d920.png)

4. 对象到数字的过程：基本上和上面一致，只不过会先调用`valueOf()`的方法。

## 转换和相等性

1. 用`==`作比较时，为了保持两端的类型一致，会做一些类型转换
2. 用`===`作比较时，并不会做任何类型转换
3. 在做转换的时候，一个值能转换成另一个值，并不意味着这两个值相等

### `===`和`==`

#### `===`严格相等

1. 不相等的情况：
   - 两个值类型不同
   - 其中一个值为`NaN`或者两个都为`NaN`（需要强调：**`NaN`和任何值都不相等，包括其本身。可以用`x!==x`来判断 x 是否为`NaN`**)。
2. 相等的情况
   - 都是`null`或者都是`undefined`
   - 两个值为数字，且数值相等（包括 0 和-0 的情况）
   - 两个值都为布尔值，且都是`true`或`false`
   - 两个值都是字符串，且它们长度一致，对应位上的值都相等
   - 两个值都是引用值，且他们指向同一个对象。数组或函数

#### `==`相等

1. 两个值类型相同，和严格相等的比较规则一样，即它们严格相等时，才会想等。
2. 两个值类型不同：
   - 一个是`null`，一个是`undefined`，认为其相等
   - 如果一个值是数字，一个是字符串，则会**先将字符串转换成数字**,然后用转换后的值进行比较
   - 如果一个值是`true`，则将其转换成 1 再比较；如果是`false`，则将其转换成 0
   - 对象和数字或字符串的比较，遵循上文的规则将对象转换成原始值再进行比较（除了日期类以外，都是先用`valueOf()`再用`toString()`）
3. 小例子：`"1" == true`，答案是相等的，分成两步：一是将`true`转换成`1`；而是将`"1"`转换成`1`。

## 显式类型转换

1. 除了`null`和`undefined`之外的任何值都有`toString()`方法
2. **数字转字符串**：
   - `Number`的`toString()`的方法可以设置基数，表示转换结果的进制：
   ```js
   var n = 17;
   binary_string = n.toString();
   binary_string = n.toString(2); // Evaluates to "10001"
   octal_string = "0" + n.toString(8); // Evaluates to "021"
   hex_string = "0x" + n.toString(16); // Evaluates to "0x11"
   ```
   - `toFixed()`, `toExponential()`和`toPrecision()`，用来控制输出的小数点位置和有效位数：
   ```js
   var n = 123456.789;
   n.toFixed(0); // "123457"
   n.toFixed(2); // "123456.79"
   n.toFixed(5); // "123456.78900"
   n.toExponential(1); // "1.2e+5"
   n.toExponential(3); // "1.235e+5"
   n.toPrecision(4); // "1.235e+5"
   n.toPrecision(7); // "123456.8"
   n.toPrecision(10); // "123456.7890"
   ```
3. **字符串转数字**：`parseInt`和`parseFloat`（这两个函数都会跳过任意数量的前置空格，尽可能解析更多的数值字符，并忽略后面的内容）

```js
parseInt("3 blind mice"); // => 3
parseFloat(" 3.14 meters"); // => 3.14
parseInt("-12.34"); // => -12
parseInt("0xFF"); // => 255
parseInt("0xff"); // => 255
parseInt("-0XFF"); // => -255
parseFloat(".1"); // => 0.1
parseInt("0.1"); // => 0
parseInt(".1"); // => NaN: integers can't start with "."
parseFloat("$72.47"); // => NaN: numbers can't start with "$"
```

其中`parseInt()`还可以设置第二个参数，用来表示第一个参数的进制

```js
parseInt("11", 2); // => 3 (1*2 + 1)
parseInt("ff", 16); // => 255 (15*16 + 15)
parseInt("zz", 36); // => 1295 (35*36 + 35)
parseInt("077", 8); // => 63 (7*8 + 7)
parseInt("077", 10); // => 77 (7*10 + 7)
```
