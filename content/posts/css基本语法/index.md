---
title: css基本语法
date: 2023-09-10
categories: ["前端"]
---


css的主要用于美化网页、布局页面，从而实现前端结构与样式的分离，
由html负责实现结构，由css负责实现样式。

## css语法

css语法由两部分构成：选择器和声明。
选择器用于指定css定义的样式作用于哪个html标签，
声明用于定义样式。
下面是一个简单的示例。

```css
h1 {
    color: red;
    font-size: 25px;
}
```

css基本语法为：

* 先写选择器指定css的作用标签，然后在花括号内写具体的样式；
* 样式以键值对的形式实现对属性的定义；
* 多个键值对之间以 ";" 分隔。

## css的引入方式

css的引入是指将编写的css代码引入到html文件，以使其生效。

css的引入方式由三种：分别为内部样式表、行内样式表和外部样式表。

### 内部样式表

内部样式表（内嵌样式表）是写到html页面内部，是将所有的 CSS 代码抽取出来，单独放到一个 `<style>` 标签中。

```html
<style>
    div {
        color: red;
    }
</style>
```

使用内部样式表需要注意，`<style>`标签理论上来说，可以放在html代码的任何地方，但一般放在`<head>`中；

### 行内样式表

行内样式表（内联样式表）是在元素标签内部的 style 属性中设定 CSS 样式。适合于修改简单样式。

```html
<div style="color: red; font-size: 12px;">青春不常在，抓紧谈恋爱</div>
```

### 外部样式表

实际开发都是外部样式表，适合于样式比较多的情况。
核心原理是：样式单独写到CSS 文件中，之后把CSS文件引入到 HTML 页面中使用。

使用该方式的步骤为：

1. 新建一个后缀为 `.css` 的样式文件，并将css代码编写在这个文件中；
2. 在html文件中使用 `<link>` 标签引入这个文件。

```html
<link rel="stylesheet" href="css文件路径">
```

## css选择器

选择器的作用是根据自己的需求，选择出对应的标签，以便将定义的样式作用于该标签。

css选择器分为基础选择器和复合选择器两大类。

基础选择器包含标签选择器、类选择器、id选择器和通配符选择器。

### 标签选择器

标签选择器是指直接选择 div 、 h1 等标签。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div {
            color: red;
        }
    </style>
</head>
<body>
    <div>
        hello, world
    </div>
    
</body>
</html>
```

### 类选择器

类选择器是首先在html标签中通过 `class` 关键字定义标签的类，
然后在css中通过 `.类名` 的方式选择标签，并定义样式。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .a {
            color: red;
        }
    </style>
</head>
<body>
    <div class="a">
        hello, world
    </div>
    
</body>
</html>
```

### id选择器

id选择器是首先在html标签中通过 `id` 关键字定义标签的id，
然后在css中通过 `#id` 的方式选择标签，并定义样式。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        #a {
            color: red;
        }
    </style>
</head>
<body>
    <div id="a">
        hello, world
    </div>
    
</body>
</html>
```

### 通配符选择器

在 CSS 中，通配符选择器使用 `*` 定义，它表示选取页面中所有元素（标签）。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            color: red;
        }
    </style>
</head>
<body>
    <div id="a">
        hello, world
    </div>
    
</body>
</html>
```

### 基础选择器总结

|  基础选择器  |             作用              |       特点       |   使用情况   |        用法        |
| :----------: | :---------------------------: | :--------------: | :----------: | :----------------: |
|  标签选择器  | 可以选出所有相同的标签，如div |  不能差异化选择  |     较多     |  `p{color:red;}`   |
|   类选择器   |    可以选出一个或多个标签     |  可根据需求选择  |    非常多    | `.nav{color:red;}` |
|   id选择器   |      一次只能选一个标签       | html中id不可重复 | 一般和js搭配 |  `#a{color:red;}`  |
| 通配符选择器 |        选择所有的标签         |  可选择全部标签  | 特殊情况使用 |  `*{color:red;}`   |

复合选择器是建立在基础选择器之上，对基本选择器进行组合形成的。

常用的复合选择器包括：后代选择器、子选择器、并集选择器、伪类选择器等。

### 后代选择器

后代选择器又称为包含选择器，可以选择父元素里面子元素。
其写法就是把外层标签写在前面，内层标签写在后面，中间用空格分隔。当标签发生嵌套时，内层标签就成为外层标签的后代。

```css
ul li {
    color: red;
}
```

### 子选择器

子元素选择器（子选择器）只能选择作为某元素的最近一级子元素。简单理解就是选亲儿子元素。

```css
div > p {
    color: red;
}
```

### 并集选择器

并集选择器可以选择多组标签，同时为他们定义相同的样式，通常用于集体声明。
并集选择器是各选择器通过英文逗号 `,` 连接而成，
任何形式的选择器都可以作为并集选择器的一部分。

```css
ul, div {
    color: red;
}
```

### 伪类选择器

伪类选择器用于向某些选择器添加特殊的效果，比如给链接添加特殊效果，或选择第1个，第n个元素。
伪类选择器书写最大的特点是用冒号 `:` 表示，
比如 `:hover` 、 `:first-child` 。

```css
a {
    color: gray;
}

a:hover {
    color: red;
}
```

> 链接伪类请按照 **LVHA** 顺序书写：`:link` 、 `:visited` 、`:hover` 、`:active`
>

```css
input: focus {
    background-color: yellow;
}
```

## css字体属性

|    属性     |       作用       |                              示例                              |
| :---------: | :--------------: | :------------------------------------------------------------: |
|  font-size  |     字号大小     |                      `p{font-size:20px;}`                      |
| font-weight |     字体粗细     |                     `p{font-weight:400;}`                      |
| font-style  |     字体样式     |                     `p{font-style:italic}`                     |
| font-family |     字体系列     |      `p{font-family:Arial,"MicrosoftYahei","微软雅黑";}`       |
|    font     | 字体属性组合写法 | `p{font:normal40025px/45pxArial,"MicrosoftYahei","微软雅黑";}` |

## css文本属性

|      属性       |       作用       |              示例               |
| :-------------: | :--------------: | :-----------------------------: |
|      color      |     文本颜色     |         `p{color:red;}`         |
|   text-align    | 文本水平对齐方式 |     `p{text-align:center;}`     |
| text-decoration |     文本修饰     | `p{text-decoration:underline;}` |
|   text-indent   |     文本缩进     |      `p{text-indent:2em;}`      |
|   line-height   |       行高       |     `p{line-height:26px;}`      |
