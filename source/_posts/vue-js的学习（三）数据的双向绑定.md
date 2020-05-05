---
title: vue.js的学习（三）数据的双向绑定
date: 2019-06-16 13:14:24
tags:
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这次来学习vue中的双向数据绑定的一种方法，使用v-model来进行数据的双向绑定，并通过其来实现一个超级简单的计算器。
<!-- more -->
### v-model
通过v-model可以实现数据的双向绑定，我们前面学习过的v-text以及v-html都只能是实现数据的单项绑定，就是将M绑定到V，有时候需要实现数据的双向绑定，这个时候就需要用到v-model来实现，基本的用法：
```html
    <input v-model="n1" type="text">
```
>指的注意的一件事情就是v-model只对表单有效，对其他元素无效，因为其他元素无法双向修改数据，只有表单可以做到用户可以修改其中的数据。


### 通过v-model来实现一个简单的计算器
```html
<body>
    <div id="app">
        <input v-model="n1" type="text">
        <select v-model="sign" name="" id="">
            <option value="+">+</option>
            <option value="-">-</option>
            <option value="*">*</option>
            <option value="/">/</option>
        </select>
        <input v-model="n2" type="text">

        <button @click="compute">=</button>
        <input v-model="res" type="text">
    </div>
    
    <script src="./../../js/vue.js"></script>
    <script>
    var vm = new Vue({
        el:'#app',
        data:{
            n1:0,
            n2:0,
            sign:'+',
            res:0,
        },
        methods: {
            compute () {
                // 这是一种方法，使用switch来判断条件
                switch (this.sign){
                    case "+" : this.res= parseFloat(this.n1) + parseFloat(this.n2); break;
                    case "-" : this.res= parseFloat(this.n1) - parseFloat(this.n2); break;
                    case "*" : this.res= parseFloat(this.n1) * parseFloat(this.n2); break;
                    case "/" : this.res= parseFloat(this.n1) / parseFloat(this.n2); break;
                }

                // 还有一种投机取巧的办法，但是不推荐使用
                // var str=this.n1+this.sign+this.n2;
                // this.res=eval(str);
            }
        },               
    })
    </script>
</body>
```

写的时候要注意不要把this给忘了。

<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/9m0ADQTKmcWcvkNKtGgqc*mJpN9hFmxgPMFXOS3EI9I!/b/dL8AAAAAAAAA&bo=6gKNAAAAAAADB0c!&rf=viewer_4">

通过两种方法都能成功实现了一个简单的计算器。

>但是要注意的一件事情是尽量不要使用第二种方法，因为第二种方法中使用的``eval()``函数会将字符串解析成代码，所以其实这是很不安全的一件事情，坏人就有机可乘可以搞破坏。

### 总结
这次学习的是vue中的``v-model``,使用这个方法可以十分轻松的实现数据的双向绑定。
