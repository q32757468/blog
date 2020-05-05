---
title: vue.js的学习（向子组件传递属性以及方法）
date: 2019-07-01 17:46:37
tags:
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前面学习了创建组件的几种方式，那么这次来学习以下父组件如何向子组件传递属性以及方法，，因为父组件只能使用父组件中的值和方法，子组件也是一样，那么有时子组件就需要用到父组件的方法该如何做呢，我发现虽然父组件无法获取到子组件内部的东西，但是子组件和父组件其实是有交叉的地方的，也就是在页面中引用子组件的那个标签，那么vue中就是通过这个标签作为中间桥梁来给子组件传递属性的，那么来试一试。
<!-- more -->

### 向子组件传递属性
向子组件传递属性值主要给组件的引用标签绑定一个中间值来实现，下面是一个例子：
```html
<body>
    <div id="app">
        <!-- 创建好一个子组件 -->
        <!-- 我们可以通过test这个桥梁来传递父组件的属性，这个属性只需要绑定在这个标签上面就好了 -->
        <test :pmsg='msg'></test>
    </div>
    <template id="test">
        <div>
            <h2>我是子组件---{{pmsg}}</h2>
        </div>
    </template>
    <script src="./../../js/vue.js"></script>
    <script>
        Vue.component('test', {
            template: '#test',
            data() {
                return {}
            },
            // 然后通过props来拿到绑定的属性
            props: ['pmsg'], //注意props是一个只读不可写的数组
        })
        var vm = new Vue({
            el: "#app",
            data() {
                return {
                    msg: '这是父组件中的一句话',
                }
            },
        })
    </script>
</body>
```
>要注意的一件事情是props是一个只读不可写的数组

<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/xGjaqlSBIzLiRLeW9HraUqS5GhBkqpwL.hP60gLb6eo!/b/dL4AAAAAAAAA&bo=CwN6AQAAAAADB1E!&rf=viewer_4">
可以看出子组件成功拿到了父组件的变量并且将其渲染到页面中。

### 子组件触发父组件的方法
子组件既然可以获取到父组件的属性，那么也可以触发到父组件的方法，触发的方式与获取父组件的属性的方式类似。
下面看演示：
```html
<body>
    <div id="app">
        <!-- 创建好一个子组件 -->
        <!-- 我们可以通过test这个桥梁来传递父组件的方法，这个属性只方法绑定在这个标签上面就好了 -->
        <test @sonalert="myalert"></test>
    </div>
    <template id="test">
        <div>
            <h2><button @click="myclick">单击我</button></h2>
        </div>
    </template>
    <script src="./../../js/vue.js"></script>
    <script>
        Vue.component('test', {
            template: '#test',
            data() {
                return {
                    msg: '这是子组件中的一句话',
                }
            },
            methods: {
                myclick() {
                    // emit英文的意思就是触发的意思，这里就可以通过emit来触发前面绑定过的方法，并且后面的参数是作为要触发的方法的参数
                    this.$emit('sonalert', this.msg)
                }
            },
        })
        var vm = new Vue({
            el: "#app",
            data() {
                return {
                    msg: '这是父组件中的一句话',
                }
            },
            methods: {
                myalert(str) {
                    // 最终执行出来的结果这里的this.msg使父组件中的
                    alert(this.msg + str);
                }
            },
        })
    </script>
</body>
```

可以看出传递方法的方式与传递属性的方式类似，都是将要传递的东西绑定在引用标签上，只不过绑定方法是用``@``来绑定，调用的方法也有区别，是用``this.$emit('sonalert', this.msg)``这种方式来触发。

这里还有值得一提的是可以通过 ``$on``方法来监听自定义事件的触发。

![调用成功](http://m.qpic.cn/psb?/V131x4904WMIoW/7PE.j8CxBZEBE1Ys4JBrRJaurnXCzXSusP*diYpnBuc!/b/dMMAAAAAAAAA&bo=GQbIAQAAAAADB*Q!&rf=viewer_4)

>还有要注意的两点是
+ 通过上面的例子我们也可以看出，虽然这个传递过来的方法是在子组件中执行的，但是实际的执行者是父组件，相当于是通过让子组件来使父组件执行它自己的方法。
+ 可以通过这种方式来给父组件传递参数，通过上面的例子我们也可以看出，我们在子组件中触发父组件的方法的时候传递了参数，而在方法执行的时候也把子组件传递的参数当作方法的参数执行了。

### 总结
灵活运用好父子组件之间传值或者传递方法对开发有很大的帮助，这也是我必须学习的，通过这一些的小demo也算是把这俩搞清楚了。
