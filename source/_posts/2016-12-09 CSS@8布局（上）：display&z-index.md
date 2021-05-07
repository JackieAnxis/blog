---
title: CSS@8布局（上）：display&amp;z-index
date: 2016-12-09 19:50:00
author: FrontEnd
tags: ["FrontEnd", "Course Notes", "CSS"]
---

# CSS 布局（上）：`display`&amp;`z-index`

### 文档流的概念

文档流指的是元素按照其在 HTML 中的位置顺序决定排布的过程，或者说在排布过程中将窗体自上而下分成一行行, 并在每行中按从左至右的顺序排放元素。
但是，在某些情况下，元素会脱离文档流，他们不会按照既定的顺序进行排布，而是按照指定的规则在文档流中浮动。

---

## `display`

`display`属性，是用来设置元素的布局方式，就是元素如何在页面上展示。

> display：**none** | **inline** | **block** | list-item | **inline-block** | **table** | inline-table | table-caption | table-cell | table-row | table-row-group | table-column | table-column-group | table-footer-group | table-header-group | run-in | box | inline-box | flexbox | inline-flexbox | flex | inline-flex

`display`可以有很多可选的值，但是，**“似乎”**最重要的就只有我加粗的这几个。比较常用的也是这几个。下面就分别介绍这几个值的含义。

### `display:inline` 行内元素

> -   默认宽度为内容宽度
> -   不可设置宽高
> -   元素之间同行显示，内部会换行

-   如果一个元素被指定为`display：inline`，那么这个元素就是一个行内元素。
-   行内元素的意思，就是这个元素**能和其他元素在同一行显示**，比如以下两个元素都是`display:inline`，他们在同一行显示：

-   **内部会换行**：虽然`inline`是一个行内元素，但是它自己内部是会换行的

-   **不可设置宽高**：就是无法为一个行内元素设置宽（`width`）高（`height`），`inline`元素的行高完全是由元素本身内容决定的

*   默认是`display:inline`的元素有：`span`, `a`, `label`, `cite`, `em`, …

---

### `display：block` 块级元素

> -   默认宽度为父元素的宽度
> -   可设置宽高
> -   元素之间换行显示

-   **默认宽度为父元素的宽度**，指的是，子元素的`margin`+`border`+`padding`+`content`的宽度和等于父元素`content-box`的宽度。默认高度是元素内容高度，值得注意。如果没有内容，那么高度即为 0。

```css
.parent {
    width: 200px;
    height: 200px;
    margin: 10px;
    padding: 50px;
    background-color: lightseagreen;
    box-sizing: border-box;
}
.child {
    height: 20px;
    padding: 20px;
    margin: 10px;
    background-color: white;
}
```

    ![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/16-12-12/76855013-file_1481555964594_d399.png)

-   默认是`display:block`的元素有：`div`, `p`, `h1`-`h6`, `ul`, `form`…

        * * *

        ### `display:inline-block` 行块元素

        > *   默认宽度为内容宽度

    > -   可设置宽高
    > -   元素之间,同行显示
    > -   后续元素宽度超过边界之后就会整块换行

-   `display:inline-block`综合了行级元素和块级元素的特点。
-   **默认宽度为内容宽度**，在未设置元素宽度的时候，元素的默认宽度就是内容的宽度，这和`inline`元素保持一致。但又同时是**可设置宽高**的。
-   元素之间，同行显示，超过边界换行
    ```css
    .parent {
        width: 100px;
        height: 60px;
        background-color: lightseagreen;
    }
    .children {
        display: inline-block;
        width: 30px;
        height: 10px;
        background-color: white;
        outline: 1px solid lightblue;
    }
    ```
-   默认是`display:inline-block`的元素有：`input`, `textarea`, `selelct`, `button`, …
-   `display:inline-block`一直有一个历史遗留问题。就是**间隙问题**，就比如下面这段代码，你可以复制这段代码到本地，你会发现，将`content-box`之外的所有内容都设为 0，元素之间仍旧有一个间隙，令人百思不得其解。

```xml
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
    .parent {
        width: 200px;
        height: 100px;
        background-color: lightseagreen;
    }
    .element {
        display: inline-block;
        width: 80px;
        height: 40px;
        margin: 0;
        padding: 0;
        border: 0;
        background-color: white;
    }
    </style>
</head>
<body>
    <div class="parent">
        <span class="element"></span>
        <span class="element"></span>
        <span class="element"></span>
        <span class="element"></span>
    </div>
</body>
</html>
```

![mark](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/jackie/20170122/191902397.png)
关于它的历史和解决方案，可以参照这篇文章[inline-block 前世今生](http://www.iyunlu.com/view/css-xhtml/64.html)，以及这篇文章提到了很多解决方案：[去除 inline-block 元素间间距的 N 种方法](http://www.zhangxinxu.com/wordpress/2012/04/inline-block-space-remove-%E5%8E%BB%E9%99%A4%E9%97%B4%E8%B7%9D/)

-   我个人而言，比较推崇在父元素上加上`font-size:0`的方法。但是这个方法也有局限性，因为要为每个子元素都再设置一遍`font-size`

---

### `display:none` 不显示

-   指的是元素不出现在文档流中，和变透明是**不一样**的，`display:none`是完完全全的消失

## 定位偏移属性

---

### `top` `bottom` `left` `right`

-   当元素的`position`属性不是`static`的时候（下文中会讲），或者添加了`float`属性，那么就可以使用标题中的这四个属性来进行定位偏移
-   默认是`auto`，也就是`top`和`left`都为 0，对齐到父元素`content-box`的左上角，以此类推。
-   这四个值都可以设置
