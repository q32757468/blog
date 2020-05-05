---
title: vue.js的学习（一）
date: 2019-06-15 21:31:48
tags:
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最近开始学习现在最火的前端开发框架vue.js，这篇博客记录我的学习过程以及学习笔记。
<!-- more -->
### vue.js的使用
先去官网下载好vue.js的文件，然后在项目中引入js文件就可以开始使用vue来进行开发了，例如这样：
```js
    <script src="./../../js/vue.js"></script>
```
然后这是一个vue的基本模板：
```html
<body>
    <!-- 这是一个普通的块 -->
    <div v-cloak id="app">
    </div>
    <script src="./../../js/vue.js"></script>
    <script>
    var vm=new Vue({
        el:'#app',// 在vue中对id为app的元素进行操作
        data:{//这里面写数据
        },
        methods: {//这里面写各种方法 
        },
    })
    </script>
</body>
```

### 插值表达式和v-text
在vue中想要对元素输入数值是非常简单的，不需要通过js或者jQuery拿到对象再通过函数进行传入数据，直接在dom元素中直接通过插值表达式就可以将vue中data中的值给到dom元素中。
比如这样：

#### 通过插值表达式
```html
<div v-cloak id="app">
        {{msg}}12314
</div>
```
```js
var vm=new Vue({
        el:'#app',
        data:{
            //这里定义一个变量来我们想要的值
            msg:'这是一个测试'
        },
        methods: {           
        },
    })   
```
我们只需要通过插值表达式将我们创建的vue对象中的data中的变量写到dom元素中就可以将变量渲染出来。

<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/qQYbwi0yWJNzAlWrAIsaadH9epSrbH*kG3HLHHqbIDc!/b/dDIBAAAAAAAA&bo=VgV1AgAAAAADBwY!&rf=viewer_4">

可以看到我们定义的变量的值成功被渲染出来了

>但是注意这样会有一个问题，就是在网速较慢的时候vue.js这个文件就会加载的比较慢，那么插值表达式就不会生效，而是直接将整个表达式当做字符串输出出来，那么这个时候我们可以通过给相应的dom元素加上``v-cloak``,然后再设置样式
```css
    [v-cloak] {
    display: none;
    }   
```

#### v-text
使用``v-text``也可以将数据渲染到dom元素，但是用法不一样
```html
 <div v-text="msg+'123423'" id="app">
</div> 
```
这样就可以实现了插值表达式一样的效果了。
>注意：v-text 后面可以通过``+``来拼接字符串

### v-html

这个用法和``v-text``一致，作用也相似，只是``v-html``可以解析html标签，但是``v-text``不能

```html
<div v-html="msg+'123423'" id="app">
</div>
```

### v-bind
``v-bind``可以给dom元素绑定属性值

``v-bind``可以简写成``:``

```html
<div v-cloak id="app">
    <input type="button" value="这是一个按钮" v-bind:title="mtitle">
</div>
```
```js
var vm=new Vue({
        el:'#app',
        data:{
            msg:'这是一个测试',
            mtitle:'这是一个测试'
        },
        methods: {           
        },
    })
```
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/oWoqza.oQdO.y45D6CeNA*JLzGxuQVGoQebnZ0AN*4M!/b/dLgAAAAAAAAA&bo=7ACBAAAAAAADB08!&rf=viewer_4">
可以看到，成功给这个按钮加上了一个我们在vue中定义的title
>``v-bind:title=``后面跟的是一个变量，所以后面也是可以拼接字符串的，许多vue中的属性也都是如此

### v-on
``v-on``与``v-bind``类似，只不过绑定的不是dom元素的属性，而是各种事件，

``v-on``可以简写成``@``

```html
<div v-cloak id="app">
    <button @click="mclick">这又是一个按钮</button>
</div>
```

```js
    var vm=new Vue({
        el:'#app',
        data:{
            msg:'这是一个测试',
            mtitle:'这是一个测试'
        },
        methods: {
            mclick () {
                alert('单击了这个按钮');
            }           
        },
    })
```

<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/Mgh9tbxP89uT9zaEpNevDAzu*KeR5w9asUwwp0IokCM!/b/dLgAAAAAAAAA&bo=DgcsAQAAAAADBwY!&rf=viewer_4">

可以看到成功触发了我们自己定义的单击事件。

### 总结
这篇博客记录学习了vue最简单初始的一些东西，一个小小的入门。