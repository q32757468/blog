---
title: vue-js的学习（ref的学习）
date: 2019-07-02 11:10:09
tags:
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在vue中是不提倡操作dom元素的,但是很多时候还是需要去操作dom元素的,那么这个时候该如何去获取dom元素,并且对他进行操作呢,如果直接通过原生js的各种选择器来操作的话其实也行,用jQuery也可以,但是在vue中都不提倡这样操作,其实我们就可以通过vue中的ref来获取以及操作dom元素.
<!-- more -->

### 来看一个简单的例子
```html
<body>
    <div id="app">
        <div class="test">
            <!-- ref就像是一个id一样可以设置在元素身上，也可以设置在组件的引用标签上 -->
            <!-- 设置在dom元素上那么在vue中拿到的就是dom元素原生对象 -->
            <h1 ref="test">这是vue实例中的一句话</h1>
            <button @click="myalert">点击我</button>
            <button @click="myalert2">点击我2</button>
            <!-- 如果设置在组件的引用标签上拿到的就是一个vue实例的对象 -->
            <test ref="test2"></test>
        </div>
    </div>

    <script src="./../../js/vue.js"></script>
    <script>
        var vm = new Vue({
            el: "#app",
            data() {
                return {

                }
            },
            methods: {
                // 可以成功对一个原生的dom对象进行了操作,输出了它里面的文本
                myalert() {
                    alert(this.$refs.test.innerText);
                },
                // 成功的对一个vue实例对象进行了操作，输出了它里面的属性
                myalert2() {
                    alert(this.$refs.test2.mgs);
                }
            },
            components: {
                test: {
                    template: "<h1>这是一个子组件</h1>",
                    data() {
                        return {
                            mgs: '这是子组件中的一句话',
                        }
                    },
                }
            },

        })
    </script>
</body>
```

通过以上例子我们就可以看出:

+ 通过在标签上设置ref来注册``$refs``对象

+ 注册在组件引用标签上与注册在dom元素上是有差别的,如果是注册在组件上那么拿到的就是一个组件实例,如果注册在dom元素上拿到的就是一个原生的dom对象.

+ 可以通过给子组件注册ref来获取到子组件中的属性以及方法并进行操作.


<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/udRf.GDUfo.MCwlGajd42SwgHrKsEJ0QfsqWaf7W81M!/b/dL4AAAAAAAAA&bo=sQRxAQAAAAADB.c!&rf=viewer_4">

通过在控制台中的查看我们也可以看出$refs是一个对象,里面保存了注册了ref的dom对象或者组件实例.

>注意是通过``this.$refs``来使用,s不能漏了,因为里面可以保存许多的内容.


### 总结
ref的使用比较简单,但是感觉作用很强大,以后开发要灵活运用.
