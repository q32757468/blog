---
title: jQuery学习（介绍与使用）
date: 2019-05-10 11:11:57
tags:
 - JS
 - jQuery
categories:
 - 学习笔记
 - jQuery学习笔记
---
### 前言
> 对于前端来说，学习掌握jQuery是基础，则将会是我学习jQuery的记录。jQuery中文文档：http://hemin.cn/jq/。

### jQuery是什么？
* jQuery是一款优秀的JavaScript库，从命名可以看出jQuery最主要的用途是用来做查询（jQuery=js+Query）.
* 在jQuery官方Logo下方还有一个副标题（write less, do more）, 体现了jQuery除了查询以外,还能让我们对HTML文档遍历和操作、事件处理、动画以及Ajax变得更加简单


### 为什么要使用jQuery？
* 强大选择器: 方便快速查找DOM元素
如上面实例所展示一样，通过jQuery查找DOM元素要比原生js快捷很多
jQuery允许开发者使用CSS1-CSS3几乎所有的选择器,以及jQuery独创的选择器
---
以下是一段jQuery代码：
```html
<body>
    <div class="test1">这是1</div>
    <div>这是0</div>
    <div id="test2">这是2</div>
    <script>
    $(document).ready(function(){
        $(".test1").css('width','100px').css('height','200px');
        $("#test2").css('background','blue');
         // 读取数据
        var $tx = $("div").eq(0).text();
        alert($tx);
        // 写入数据
        $("div").eq(0).text("新的数据");

    });
    </script>
</body>
```
**通过以上代码可以看出，jQuery查找元素要快的多，方便的多，并且jQuery可以不断的调用jQuery对象的方法，并且可以一次操作。读取数据也是一个函数，使用比较方便**

* 事件处理
* DOM操作(C增U改D删)
* 样式操作
* 动画
* 丰富的插件支持
* 浏览器兼容(前端开发者痛点) 

### 如何使用jQuery？
下载过后这样调用就可以了：
```html
<script src="../jquery-1.12.4.js"></script>
```
