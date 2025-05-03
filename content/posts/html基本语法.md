---
title: html基本语法
date: 2023-09-08
categories: ["前端"]
---


html是一种超文本标记语言（Hyper Text Markup Language），它描述网页基本结构的一种语言。

与 `Java` 等语言不同，`html` 语言不是编程语言，而是一种标记语言。

所谓 **超文本** 主要体现在：

- 它可以在内容中定义图片、声音、音频等多媒体信息；
- 它可以给网页内容增加链接信息，实现从一个文件链接到另一个文件，即超链接。


## html语法规范

### 基本语法

1. HTML 标签是由尖括号包围的关键词，例如 `<html>`。
2. HTML 标签通常是成对出现的，例如 `<html>` 和 `</html>` ，我们称为双标签。
   标签对中的第一个标签是开始标签，第二个标签是结束标签。
3. 有些特殊的标签必须是单个标签（极少情况），例如 `<br />`，我们称为单标签。

### 标签关系

标签关系主要分为两种，分别是 **包含关系** 和 **并列关系**。

包含关系是指一个标签在另一个标签的内部，如：

```html
<head>
    <title> <title>
</head>
```

并列关系是指两个标签互不包含，彼此并列存在，如：

```html
<head> </head>
<body> </body>
```

## html基本结构标签

每个网页都会一个基本的结构标签（或称骨架标签），
网页的具体内容也是在这个基本结构标签的基础之上进行编写。

一个标准的基本html标签结构如下：


```html
<html>
    <head>
        <title></title>
    </head>
    <body>
        在这里写页面
    </body>
</html>
```

## html语法

* `<html></html>`
  
  html标签，页面中最大的标签，称为根标签；

* `<head></head>`

  文档的头部，必须包含title标签；

* `<title></title>`
  
  文档标题；

* `<body></body>`
  
  文档主题，页面内容在该标签内编写；

* `<!DOCTYPE html>`
  
  声明文档类型，位于`<html>`标签之前，用于告诉浏览器使用哪种html版本来显示网页，该标签不是html标签，属于文档类型声明标签；

* `<meta charset="UTF-8" />`
  
  声明编码字符集；

* `<h1> - <h6>`
  
  标题标签

* `<p>`
  
  段落标签

* `<br />`

  换行标签

* `<div></div>`

  盒子容器标签，用于布局，一行只能放一个`<div>`

* `<span></span>`
  
  盒子容器标签，用于布局，一行可以放多个`<span>`

* `<img src="图片url" />`
  
  图片标签

  包含属性：src(图片路径)、alt(替换文本，图像不能正常显示时显示该文字)、
  title(提示文本，鼠标放到图片上显示改文字)、width(图像宽度)、height(图像高度)、border(图像边框粗细)

* `<a></a>`
  
  超链接标签，用法如下：

  * `<a href="http://www.baidu.com">链接到百度</a>`
  * `<a href="index.html">链接到内部index.html页面</a>`
  * `<a href="#">空链接</a>`
  * `<a href="#two">链接到页面内其他位置，需要实现做好锚点</a>`

* <!--注释-->
  
  注释内容

* 特殊字符
 
| 特殊字符 |   描述   |   代码   |
| :------: | :------: | :------: |
|    _     |  空格符  |  &nbsp;  |
|    <     |  小于号  |   &lt;   |
|    >     |  大于号  |   &gt;   |
|    &     |   和号   |  &amp;   |
|    ￥     |  人民币  |  &yen;   |
|    ©     |   版权   |  &copy;  |
|    ®     | 注册商标 |  &reg;   |
|    ℃     |  摄氏度  |  &deg;   |
|    ±     |  正负号  | &plusmn; |
|    ✖     |   乘号   | &times;  |
|    ➗     |   除号   | &divide; |
|    ²     |   平方   |  &sup2;  |
|    ³     |   立方   |  &sup3;  |


* 表格标签
  
```html
    <table>
        <thead>
            <tr>
                <th>表头</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>内容</td>
            </tr>
        </tbody>
    </table>
```

`<table>`用于声明表格，`<tr>`用于声明行，`<th>`用于声明表头，`<td>`用于声明单元格

  表格属性：

  * align：取值有left、center、right，设置表格对齐方式；
  * border: 设置表格单元格的边框；
  * cellpadding: 单元格边沿与其内容之间的空白；
  * cellspacing: 单元格之间的空白；
  * width：表格的宽度。

  合并单元格使用 `<td rowspan="2">内容</td>` 和 `<td colspan="2"></td>` 。

* 列表标签
  
  列表标签分为三类：无序列表、有序列表、自定义列表。

  * 无序列表
    
    ```html
    <ul>
        <li>列表项1</li>
        <li>列表项2</li>
        <li>列表项3</li>
    </ul>
    ```

  * 有序列表
    
    ```html
        <ol>
            <li>列表项1</li>
            <li>列表项2</li>
            <li>列表项3</li>
        </ol>
    ```

  * 自定义列表
    
    ```html
    <dl>
        <dt>名词1</dt>
            <dd>名词1解释1</dd>
            <dd>名词1解释2</dd>
    </dl>
    ```

* 表单标签
  
  一个完整的表单通常由 **表单域** 、 **表单控件** 和 **提示信息** 三部分组成。

  三个组成部分的大致用法如下：

  ![](../static/img/../../../_static/img/frontend/html/Snipaste_2023-05-19_12-53-47.png)

  * 表单域
    
    ```html
    <form action="url地址" method="提交方式" name="表单域名称">
        各种表单元素控件
    </form>
    ```

  * 表单控件
  
    * input输入表单控件

        ```html
        <input type="属性值" />
        ```
    
      type属性的属性值如下：

        |  属性值  |        说明        |
        | :------: | :----------------: |
        |  button  |      点击按钮      |
        | checkbox |       复选框       |
        |   file   |      文件上传      |
        |  hidden  |   隐藏的输入字段   |
        |  image   | 图像形式的提交按钮 |
        | password |        密码        |
        |  radio   |      单选按钮      |
        |  reset   |      重置按钮      |
        |  submit  |      提交按钮      |
        |   text   |     文本输入框     |

      input表单控件的属性：

        |   属性    |        说明        |
        | :-------: | :----------------: |
        |   name    |  input元素的名称   |
        |   value   |   input元素的值    |
        |  checked  | 首次加载时选中元素 |
        | maxlength |    最大字符长度    |

      关于以上属性，以下几点需要注意：

      1. name和value是每个元素都有的属性值，主要作用是供后台人员使用；
      2. 单选框和复选框要求必须有相同的name值；
      3. checked主要针对单选框和复选框；
      4. value可以用来设置初始值。

      `<label>`标签为input元素的标注。用法如下：


        ```html
        <label for="sex">男</label> 
        <input type="radio" name="sex" id="sex" />
        ```
    
      注意：`<label>` 标签的 for 属性应当与相关元素的 id 属性相同

    * select下拉表单元素

    ```html
    <select> 
        <option>选项1</option> 
        <option>选项2</option> 
        <option>选项3</option> 
        ...
    </select>
    ```
      
      注意：

      1. `<select>`中至少包含一对`<option>`；
      2. 在`<option>`中定义 selected="selected"时，当前项即为默认选中项。

    * textarea文本域元素
      
    ```html
    <textarea rows="3" cols="20">
        文本内容
    </textarea>
    ```
