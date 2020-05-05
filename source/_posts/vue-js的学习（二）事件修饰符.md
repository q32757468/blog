---
title: vue.js的学习（二）事件修饰符
date: 2019-06-16 11:03:05
tags:
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这次的笔记来学些一下vue中的事件修饰符，通过事件修饰符可是实现一些简单的功能，比如阻止冒泡之类的。
<!-- more -->

一下是一段运用了事件修饰符的代码：
```html
<style>
div{
    padding: 50px;
}
</style>
<body>
    <div @click='outerclick' id="app" style="background-color: blue">
        <div @click='innerclick' style="background-color: red">
            <button @click='btnclick'>这是一个测试按钮</button>
        </div>
        <a @click.prevent.once href="https://www.baidu.com">这是去百度的链接</a>
    </div>

    <script src="./../../js/vue.js"></script>
    <script>
    var vm=new Vue({
        el:'#app',
        data:{// 通过在data中设置变量      
            msg:'这是一个测试内容',
        },
        methods: {
           btnclick(){
               alert('这是一个按钮');
           },
           innerclick(){
               alert('这是里面的div')
           },
           outerclick(){
               alert('这是外面的div')
           }
        },
    }) 
    </script>
</body>
```

### .stop
 通过``.stop``可以阻止事件向上冒泡

 ```
<button @click.stop='btnclick'>这是一个测试按钮</button>
 ```

我们只需要这样设置就可以阻止事件向上冒泡

### .self
 通过``.self``可以阻止事件向上冒泡
```
    <div @click.self='innerclick' style="background-color: red">

```
我们只需要这样设置就使这个里面的div在按钮进行事件冒泡的时候不会触发它的单击事件，只会触发自己的事件。

### .prevent
通过``.prevent``就可以阻止默认行为
```
        <a @click.prevent href="https://www.baidu.com">这是去百度的链接</a>
```
通过以上代码就可以实现阻止默认行为，但我们单击超链接的时候就不会进行跳转

### .once
通过``.once``就可以使事件修饰符只起作用一次
```
        <a @click.prevent.once href="https://www.baidu.com">这是去百度的链接</a>
```
通过以上代码，当我们单击超链接的时候第一次不会进行跳转，但是再次单击的时候就会进行跳转了
>其他事件修饰符也适用

### 总结
这次学习了vue中的事件修饰符