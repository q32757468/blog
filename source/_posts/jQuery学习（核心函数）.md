---
title: jQuery学习（核心函数）
date: 2019-05-10 13:08:50
tags:
 - JS
 - jQuery
categories:
 - 学习笔记
 - jQuery学习笔记
---
### 前言
> 记录了jQuery的核心函数以及jQuery对象，jQuery的静态方法的使用。
<!-- more -->
### jQuery核心函数
#### jQuery核心函数一共3大类4小类
* jQuery(callback) 
当DOM加载完成后执行传入的回调函数
* jQuery([sel,[context]]) 
接收一个包含 CSS 选择器的字符串，然后用这个字符串去匹配一组元素,并包装成jQuery对象,也就是jQuery的选择器。
* jQuery(html,[ownerDoc]) 
根据 HTML 标记字符串，动态创建DOM 元素
```html
<script>
$(function(){
    var $text=$("<p>这是一段文字</p>");
    $("body").append($text);
});
</script>
```        
### jQuery对象
* jQuery对象的本质是什么? 
jQuery对象的本质是一个伪数组
```html
<script>
        var $div = $('div');
        console.log($div);
        // [object Object]: {0: Object, context: Object, length: 1, prevObject: Object, selector: "div"}
</script>
```
* 什么是伪数组? 
有0到length-1的属性,并且有length属性

### jQuery静态方法
* 什么是静态方法? 
静态方法对应的是对象方法,对象方法用实例对象调用,而静态方法用类名调用

#### jQuery.holdReady(hold) 
暂停或者恢复jQuery.ready()事件
传入true或false
```html
<script>
        // 使用$直接调用,是静态方法
        $.holdReady(true);
        // 暂停住了
        $(function () {
            $("#first").click(function () {
                alert("我是你想要的弹窗");
            });
        });
    </script>
</head>
<body>
<button id="first">点击测试弹出</button>
<button id="second">解除延迟</button>
<script>
    $("#second").click(function(){
        $.holdReady(false);
        // 单击会触发这个，然后恢复
    });
</script>
```

#### $.each(object,[callback]) 
遍历对象或数组
优点统一遍历对象和数组的方式
回调参数的顺序更符合我们的思维模式

#### $.map(arr|obj,callback) 
遍历对象或数组,将回调函数的返回值组成一个新的数组返回

#### $.trim(str) 
去掉字符串起始和结尾的空格。

#### $.isArray(obj)
判断是否是数组

#### $.isFunction(obj) 
判断是否是函数

#### $.isWindow(obj) 
判断是否是window对象