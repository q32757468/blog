---
title: vue.js的学习（六）v-if和v-show
date: 2019-06-16 17:19:12
tags:
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这次来学习vue中隐藏元素的两种方法，一种是v-if，另一种是v-show，同时明白二者之间的区别，一件分别适用于哪种情况。
<!-- more -->

### 示例

```html
<body>
    <div id="app">
        <!-- 通过两种方式都可以实现元素的隐藏，但是我们从控制台检查元素就可以看出二者之间的区别 -->
        <button @click="flag=!flag">点击显示/不显示</button>
        <p v-if="flag">
            这是用v-if控制的元素
        </p>
        <p v-show="flag">
            这是用v-show控制的元素
        </p>
    </div>

    <script src="./../../js/vue.js"></script>
    <script>
        var vm = new Vue({
            el: '#app',
            data() {
                return {
                    flag:true,
                }
            },
            methods: {
                toggle (){
                    this.flag=!this.flag;
                }
            },
        })
    </script>
</body>
```
通过以上代码，我们在页面中单击切换按钮就可以隐藏或者显示两个分别被``v-if``和``v-show``控制的元素。

### 二者之间的区别

* 通过检查元素我们可以清除的看到，当我们将元素进行隐藏的时候，``v-if``是将元素进行删除，而``v-show``是将元素的``display``设置为none，从而将元素隐藏。

+ 那么我们什么时候用``v-if``什么时候用``v-show``呢
    - 因为``v-if``是将元素进行添加和删除，所以在进行这个过程的时候会比较消耗资源，所以不推荐在频繁要触发``v-if``的时候使用
    - 而``v-show``是设置元素的display，那么无论元素一开始是否显示在页面上都需要创建元素，所以在渲染页面的时候会相较于``v-if``更加占用资源，所以不推荐在很少需要用到``v-show``来进行切换的时候使用。

### 学到的一些新东西
当我们需要频繁的在两种状态之间进行切换的时候我们可以使用下面这种方法来控制
```
   flag:true,
```

```
    this.flag=!this.flag;
```
就不需要像以前的思路那样设置一个数字的循环，然后判断是否能被2整除来进行两种状态之间的切换。


当函数的代码连非常的小的时候我们就可以直接在调用的时候写，像下面这样：
```
    <button @click="flag=!flag">点击显示/不显示</button>
```

### 总结
二者之间的区别在于渲染的方式不一样，在切换不频繁的情况下使用``v-if``，在切换频繁的情况下使用``v-show``

