---
title: jQuery学习（事件处理）
date: 2019-05-14 10:20:15
tags:
 - JS
 - jQuery
categories:
 - 学习笔记
 - jQuery学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;jQuery中也有许多事件处理的函数，功能强大，使用起来也比原声js要更加的简单和方便，这次学习以下jQuery的事件处理函数与冒泡行为以及自定义事件等等，以及动手实践，编写实例。
<!-- more -->
### 事件绑定
**jQuery中事件绑定有两种方式**

* eventName(function(){})

绑定对应事件名的监听,   例如：$('#div').click(function(){});
优缺点：编写方便，但是不支持所有事件与自定义事件，但常用的事件基本上都是支持的。


* on(eventName, funcion(){})

通用的绑定事件监听, 例如：$('#div').on('click', function(){});
优缺点：支持所有事件，但是编码的时候事件名要自己敲，但是最强大，支持所有事件与自定义事件。

### 事件解绑
* jQuery中可以通过off(eventName,function);解绑事件

### 获取事件坐标
当事件被触发时,系统会将事件对象(event)传递给回调函数,通过event对象我们就能获取时间的坐标，获取坐标有三种方式：
* event.offsetX, event.offsetY      相对于事件元素左上角
* event.pageX, event.pageY          相对于页面的左上角
* event.clientX, event.clientY      相对于视口的左上角

**实例示例代码**
```html
<style>
button{
    margin-top: 600px;
}
</style>
<body>
    <button>这是按钮一</button>
    <button>这是按钮二</button>
    <button>这是按钮三</button>
    <button>这是按钮四</button>
    <script>
    $(function(){
        $('button').eq(0).click(function(Event){
            alert(Event.offsetY);
            // 结果：10
        })
        $('button').eq(1).click(function(Event){
            alert(Event.pageY);
            // 结果：626
        })
        $('button').eq(2).click(function(Event){
            alert(Event.clientY);
            // 结果：630
        })
    })
    </script>
</body>
```
---
### 阻止事件冒泡
* 什么是事件冒泡
·事件冒泡就是同属说就是事件从里往外的一个触发过程
```html
<style>
    .father {
        width: 400px;
        height: 400px;
        background-color: red;
    }

    .son {
        width: 200px;
        height: 200px;
        background-color: blue;
    }
</style>

<body>
    <div class="father">
        这是爸爸
        <div class="son">
            这是儿子
        </div>
    </div>
    <script>
        $(function () {
            $('.father').click(function(){
                alert('这是爸爸');
            })
            $('.son').click(function(){
                alert('这是儿子');
            })
        })
    </script>
</body>
```
当我单击了儿子之后，由于事件冒泡，事件会由里往外触发，所以**先弹出儿子**，**再弹出爸爸**，很多时候这都不是我们想要的结果，我们通常只是需要触发儿子的事件，所以这个时候我们就需要阻止事件冒泡。
* 如何阻止事件冒泡?
event.stopPropagation()
```html
$(function () {
            $('.father').click(function(){
                alert('这是爸爸');
            })
            <!-- 传递了事件对象 -->
            $('.son').click(function(event){
                event.stopPropagation();
                <!-- 组阻止了事件冒泡 -->
                alert('这是儿子');
            })
        })
```
此时我们再次单击儿子就会发现就**只弹出了儿子的单击事件，并没有触发父亲的事件**，就达到了我们的效果

---

### 阻止默认行为
* 什么是默认行为
·就是一些元素默认自带的行为，比如单击超链接会自动跳转等等。
* 如何阻止默认行为
event.preventDefault()
```html
    <a href="www.baidu.com">这是一个跳转到百度的超链接</a>
    <script>
    $(function(){
        $('a').click(function(event){
            event.preventDefault();
            // 此时单击超链接时就不会进行跳转了
        })
    })
    </script>
```
---

### 自动触发事件
* 什么是自动触发事件?
·通过代码控制事件, 不用人为点击/移入/移除等事件就能被触发

* 自动触发事件方式
$("selector").trigger("eventName");
触发事件的同时会触发事件冒泡
触发事件的同时会触发事件默认行为
$("selector").triggerHandler("eventName");
触发事件的同时不会触发事件冒泡
触发事件的同时不会触发事件默认行为

以triggerHandler()为例：
```html
<body>
 <button>这是一个按钮</button>
 <script>
 $(function(){
    $('button').eq(0).click(function(){
        alert('我单击了按钮');
        // 原本要单击才会弹出内容
    })
    $('button').triggerHandler('click');
    // 现在在网页出现后就会弹出
 })
 </script>
</body>
```
---

### 事件命名空间
我们都知道jQuery可以给同一元素绑定多个相同的事件，且都能够触发，那么在多人开发中就可能会遇到多人对同意元素操作的情况，那么为了方便管理与区分，我们就可以在注册事件的时候使用命名空间
* 格式：eventName.命名空间，如 click.hsl()
* 添加事件命名空间的前提条件
1、事件是通过on来绑定的，也就是这种方法`on(eventName, funcion(){})`
2、通过trigger触发事件
* 注意点(面试题):
·不带命名空间事件被trigger调用,会触发带命名空间事件
·带命名空间事件被trigger调用,只会触发带命名空间事件
·下级不带命名空间事件被trigger调用,会冒泡触发上级不带命名空间和带命名空间事件
·下级带命名空间事件被trigger调用,不会触发上级不带命名空间事件
·下级带命名空间事件被trigger调用,会触发上级带命名空间事件

### 自定义事件
* 什么是自定义事件？
自定义事件，可以自己定义事件的名称，然后通过这个名称还能触发相应的功能
* 自定义事件的前提
1、事件是通过on来绑定的，也就是这种方法`on(eventName, funcion(){})`
2、通过trigger触发事件
例如：
```html
<body>
 <button>这是一个按钮</button>
 <script>
 $(function(){
    $('button').on('hsl',function(){
        alert('这是一个自定义事件！');
        // 自定义了一个名为hsl的事件
    })
    $('button').triggerHandler('hsl');
    // 通过trigger来触发
 })
 </script>
</body>
```
---
### 事件委托
* 事件委托的原理
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;事件委托是通过冒泡机制来实现的，例如有一个父元素，里面有许多子元素，我们无需给这众多的子元素都添加单击事件，我们只需给父级元素添加一个单击事件，那么我们在单击子元素的时候，就会网上冒泡，执行父元素的事件，就相当于是委托父级元素来执行一样。

* 事件委托的好处
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**减少监听数量：**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;添加到页面上的事件处理程序数量将直接关系到页面的整体运行性能，因为需要不断的与dom节点进行交互，访问dom的次数越多，引起浏览器重绘与重排的次数也就越多，就会延长整个页面的交互就绪时间
每个监听的函数都是一个对象，是对象就会占用内存，对象越多，内存占用率就越大，自然性能就越差
... ...
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**新增元素自动有事件响应处理：**
默认的情况下新增元素是无法相应增添前就有的事件。就可以通过冒泡到原本就有的父级元素来触发事件。

* jQuery中如何添加事件委托：
delegate(selector,[type],[data],fn)
下面看一个例子：
```html
<body>
    <ul class="father">
        <li class="son">这是内容</li>
        <li class="son">这是内容</li>
        <li class="son">这是内容</li>
    </ul>
    <button>增加li</button>
    <script>
    $(function(){
        $('.son').click(function(){
            alert('我是原本就有的li');
            // 单击li时应该会有弹窗
        })
        $('button').click(function(){
            $('.father').append('<li>我是新增的li</li>')
            // 单击新增li
        })
    })
    </script>
</body>
```
运行以上代码，我们可以发现一个问题，单击原有的li时会有弹窗，但是单击后面新创建的li时却并没有弹窗，因为我们没有给后面创建的li添加单击事件。

此时我们使用事件委托就可以解决问题：
```html
<body>
    <ul class="father">
        <li class="son">这是内容</li>
        <li class="son">这是内容</li>
        <li class="son">这是内容</li>
    </ul>
    <button>增加li</button>
    <script>
    $(function(){
        // $('.son').click(function(){
        //     alert('我是原本就有的li');
        // })
        // 原本的单击事件也可以不需要了
        $('button').click(function(){
            $('.father').append('<li>我是新增的li</li>')

        })
        // 通过事件委托，新添加的也能有弹窗
        $('.father').delegate('li','click',function(){
            alert('我是li');
        })
    })
    </script>
</body>
```
改用事件委托后，不管是新添加的还是本身就存在的li，都能成功的弹窗(原理是向上冒泡，所以要支持冒泡的事件才能有效果)


