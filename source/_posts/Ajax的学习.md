---
title: Ajax的学习
date: 2019-05-26 14:44:01
tags:
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ajax技术可以说是随处可见，在网页的交互方面基本上都用到了Ajax，可以说Ajax作用是十分的强大，通过 Ajax，因特网应用程序可以变得更完善，更友好。所以作为正在学习的我来说，完整学习Ajax以及完全掌握Ajax是十分重要的。
<!-- more -->

### 什么是Ajax呢？
* Ajax 即“Asynchronous Javascript And XML”（异步 JavaScript 和 XML），是指一种创建交互式网页应用的网页开发技术。
* Ajax = 异步 JavaScript 和 XML 或者是 HTML（标准通用标记语言的子集）。
* Ajax 是一种用于创建快速动态网页的技术。
* Ajax 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。
* 通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。
* 传统的网页（不使用 Ajax）如果需要更新内容，必须重载整个网页页面。

### Ajax所涉及的相关知识
* get方式提交数据与post方式提交数据的异同点
1、通过get方式提交数据会把数据放在url地址栏中，而使用post方式提交数据则会把数据放到请求头中，可以在调试工具的network栏中看到这些数据
2、get请求由于浏览器对于url地址栏长度的限制，所以导致get1请求发送数据会有长度限制，要支持ie，那么最大长度就为2083byte


* 原生js实现Ajax可以参考<a href="http://www.w3school.com.cn/ajax/ajax_xmlhttprequest_create.asp">这里</a>

* 由于原生js实现Ajax过程比较多，这里不做过多介绍，了解即可，通常使用jQuery中的ajax方法，比较方便快捷的使用Ajax。

### jQuery中的Ajax
* jQuery 提供多个与 AJAX 有关的方法。
* 通过 jQuery AJAX 方法，您能够使用 HTTP Get 和 HTTP Post 从远程服务器上请求文本、HTML、XML 或 JSON - 同时能够把这些外部数据直接载入网页的被选元素中。
* jQuery.ajax([settings]) 这是jQuery中的Ajax的基本用法。

**settings最常用的参数**
* type
类型：String
默认值: "GET")。请求方式 ("POST" 或 "GET")， 默认为 "GET"。注意：其它 HTTP 请求方法，如 PUT 和 DELETE 也可以使用，但仅部分浏览器支持。
* url
类型：String
默认值: 当前页地址。发送请求的地址。
* data
类型：String
发送到服务器的数据。将自动转换为请求字符串格式。GET 请求中将附加在 URL 后。查看 processData 选项说明以禁止此自动转换。必须为 Key/Value 格式。如果为数组，jQuery 将自动为不同值对应同一个名称。如 {foo:["bar1", "bar2"]} 转换为 '&foo=bar1&foo=bar2'。
* error
类型：Function
默认值: 自动判断 (xml 或 html)。请求失败时调用此函数。
有以下三个参数：XMLHttpRequest 对象、错误信息、（可选）捕获的异常对象。
如果发生了错误，错误信息（第二个参数）除了得到 null 之外，还可能是 "timeout", "error", "notmodified" 和 "parsererror"。
这是一个 Ajax 事件。
* success
类型：Function
请求成功后的回调函数。
参数：由服务器返回，并根据 dataType 参数进行处理后的数据；描述状态的字符串。
这是一个 Ajax 事件。
* 除了以上这些外，jQuery中Ajax的settings还有很多参数，具体可以参考<a href="http://www.w3school.com.cn/jquery/ajax_ajax.asp">这里</a>

### 通过jQuery中的Ajax来写两个简单的例子
* 通过get方式来向远程服务器提交数据。
html代码
```html
<body>
    <!-- 通过一个按钮的单击事件来触发ajax -->
    <button>这是一个按钮，点击发送Ajax请求</button>
    <script>
        $(function(){
            var $bt=$('button');
            // 为按钮绑定单击事件
            $bt.click(function(){
                // 调用jQuery中的Ajax
                $.ajax({
                    type:'get',
                    url:'./php/ajax-get.php',
                    data:'name=hsl&age=18',
                    success:function(msg){
                        alert(msg)
                    },
                    error:function(xhr){
                        alert(xhr)
                    }                  
                })
            })
        })
    </script>
</body>
```
php代码
```php
<?php
    echo '这是返回的名字'.$_GET['name'];
    echo '这是返回的年龄'.$_GET['age'];
?>
```
根据代码我们可以看出当我们单击按钮的时候就会触发单击事件调用ajax，给我们返回后端处理过后的数据，那结果究竟会是怎样呢。
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/tepFyRHq8E3mxrioO.DorS3OftIWsQOzaJozrNmPjn8!/b/dEkBAAAAAAAA&bo=gAcSBAAAAAADB7M!&rf=viewer_4">
通过图片我们可以看出Ajax成功返回了我们想要的数据，并且我们可以看到这个php文件的url附加了我们的参数信息。
* 通过post方式来向远程服务器提交数据。
html代码
```html
<body>
    <button>这是一个发送post请求的按钮</button>
    <script>
    $(function(){
        $('button').click(function(){
            $.ajax({
                url:'./php/ajax-post.php',
                type:'post',//这里的传输方式改成了post
                data:'name=hsl&age=18',
                success:function(msg){
                    alert(msg);
                },
                error:function(xhr){
                    alert(xhr);
                }             
            })
        })
    })
    </script>
</body>
```
php代码
```php
<?php
echo $_POST['name'];
echo $_POST['age'];
?>
```
可以看出上面的代码主要就是将``type`` 从``get``改成了``post``，那效果会怎样呢.
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/1.LjjfZdyKXKBtldNxrnXL3Uzc3*ukvvi07p41je1jE!/b/dAcBAAAAAAAA&bo=gAcSBAAAAAADF6M!&rf=viewer_4">
从图片中我们可以看出使用post方式也成功获取了我们想要的数据，但是我们不能在url中看见我们传过去的参数，但是能在报文头中看见这些参数，因为不像get方式会在url栏传参数，所以也没有长度的限制。

### 总结
通过这次的学习，掌握了Ajax的基本用法，也通过实例了解了get方式与post方式的一些差别，二者以后要根据实际情况来使用。