---
title: vue.js的学习（四）添加样式
date: 2019-06-16 15:01:03
tags:
 - JS
 - VUE
 - CSS
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这次学习了通过vue来给元素添加样式,前面我们已经学习过了给元素绑定属性,我们就可以通过这种方法来给元素添加样式.主要通过两种方法,一种是给元素绑定class,一种是给元素绑定style.
<!-- more -->

先看页面代码:
```html
<style>
.big{
    font-size: 40px;
}
.color{
    color: red;
}
.thin{
    font-weight: 200;
}

</style>
<body>
    <div id="app">
        <!-- 第一种添加样式类 ,注意要加引号-->
        <p :class="['big','thin']">这是一段测试文字</p>
        <!-- 第二种 数组中嵌套对象，通过对象来控制是否引用相应的类   ,在对象中可以省略引号，除非有 -   -->
        <p :class="['big',{thin:true,color:flag}]">这是一段测试文字</p>
        <!-- 第三种，直接使用对象 -->
        <p :class="{thin:true,big:flag}">这是一段测试文字</p>
        <!-- 也可以直接在变量中写对象,直接调用变量 -->
        <p :class="mclass">这是一段测试文字</p>

        <!-- 通过绑定style来添加样式 -->
        <p :style="{color:'red','font-size':'40px'}">这是一段测试文字</p>
        <p :style="mstyle">这是一段测试文字</p>
        <p :style="[mstyle,mstyle2]">这是一段测试文字</p>

    </div>

    <script src="./../../js/vue.js"></script>
    <script>
    var vm = new Vue({
        el:'#app',
        data:{
            flag:true,
            mclass:{
                thin:true,
                color:true,
                big:true,
            },
            mstyle:{
                color:'red',
                'font-size':'40px',
                'font-weight':200,
            }
        },
        methods: {        
        },
    })     
    </script>
</body>
```
### 绑定class来添加样式
#### 通过数组
通过给元素绑定class来添加样式:
```
        <p :class="['big','thin']">这是一段测试文字</p>
```
给通过给class绑定一个数组来添加class样式
>注意,数组里面的元素都需要加上引号

#### 通过数组中嵌套对象
```
        <p :class="['big',{thin:true,color:flag}]">这是一段测试文字</p>
```
通过对象来添加class需要通过``true``或者是``false``来控制是否绑定相应class类
>这里可以直接给``true``或者是``false``也可以通过变量来传递``true``或者是``false``,比如这里通过了一个变量``flag``

#### 通过对象
既然可以通过数组中嵌套对象,那么就可以直接使用对象来绑定class
```
<p :class="{thin:true,big:flag}">这是一段测试文字</p>
```
直接通过对象来绑定class也可以将对象设置在一个变量中,通过直接绑定这个变量也可以实现.
```
<p :class="mclass">这是一段测试文字</p>
```
>在对象中的键值对中的键可以加引号也可以不加引号,但是名字中有``-``就必须要加引号.

### 绑定style来添加样式

* 通过对象来设置:

```
        <p :style="{color:'red','font-size':'40px'}">这是一段测试文字</p>
```

* 将对象设置到变量通过变量来设置
```
        <p :style="mstyle">这是一段测试文字</p>
```

* 通过数组来同时设置多个变量
```
        <p :style="[mstyle,mstyle2]">这是一段测试文字</p>
```

### 来看看设置之后的效果
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/yIavscNRQ9xSYRiKT2*Wtft3fQGq8zmitSDApwflzYk!/b/dL8AAAAAAAAA&bo=vQavAwAAAAADBzU!&rf=viewer_4">
可以看到全部都生效了

### 总结
以上就是在vue中设置样式的一些方法

