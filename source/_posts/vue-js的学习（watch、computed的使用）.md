---
title: vue-js的学习（watch、computed的使用）
date: 2019-07-04 10:04:56
tags:
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这次来学习以下vue中的两个选项，watch和computed 这两个是用来监听数据的变化的，但是又有区别。以前我监听数据的变化都是通过原生js的 oninput或者是 keyup 事件来判断数据是否发生变化，但是很显然在vue中这样是不够哦高效的，那么在vue中正确使用 watch 和 computed 可以大大的提高我们的开发效率。
<!-- more -->

### watch的使用 
watch是vue实例中的一个选项，是一个对象，键是需要观察的表达式，值是对应回调函数。值也可以是方法名，或者包含选项的对象。Vue 实例将会在实例化时调用 $watch()，遍历 watch 对象的每一个属性。

可以像这样使用：

+ 监听文本框数据

```
<body>
    <div id="app">
        <input type="text" v-model="a">+
        <input type="text" v-model="b">=
        <input type="text" v-model="c">
    </div>

    <script src="./../../js/vue.js"></script>
    <script>
        var vm = new Vue({
            el: "#app",
            data() {
                return {
                    a: '',
                    b: '',
                    c: '',
                }
            },
            methods: {
                watchAB() {
                    this.c = this.a + '-' + this.b;
                }
            },
            watch: {
                // 可以直接写函数，直接写函数的话可以有两个参数，一个是改变之前的值，一个是改变之后的值
                // a: function (newVal, oldVal) {
                //     this.c = newVal + '-' + this.b
                // },
                // b: function (newVal, oldVal) {
                //     this.c = this.a + '-' + newVal
                // },

                // 也可以直接调用已经有的方法，但是不能向上面一样有两个参数
                a: "watchAB",
                b: "watchAB",
            },
        })
    </script>
</body>
```

+ 监听路径变化
    - watch不仅仅能够监听文本框的数据变化，它能够监听到vue中一切数据的变化，十分的强大
    - 例如监听路径的变化：
    ```
                //这里可以不用加this
                "$route.fullPath": function (newVal, oldVal) {
                    //这里用path或者是fullPath来判断都可以，但是要注意大小写
                    if (newVal == "/test") {
                        alert('跳转到了test组件')
                    }
                }
    ```


>注意，不应该使用箭头函数来定义 watcher 函数,理由是箭头函数绑定了父级作用域的上下文，所以 this 将不会按照期望指向 Vue 实例

### computed的使用
同样一个例子我们也可以使用 computed 来完成，我们也可以看出他们之间的区别。

```
<body>
    <div id="app">
        <input type="text" v-model="a">+
        <input type="text" v-model="b">=
        <input type="text" v-model="c">
    </div>

    <script src="./../../js/vue.js"></script>
    <script src="./../../js/vue-router.js"></script>
    <script>
        var vm = new Vue({
            el: "#app",
            data() {
                return {
                    a: '',
                    b: '',
                    //这里不再需要对c进行定义
                }
            },
            methods: {

            },
            computed: {
                // 如果在computed中要对某个数据进行监听计算，那么这个数据需要放到computed中来进行定义
                c: function () {
                    // 然后还需要将我们需要的结果返回出去
                    return this.a + "-" + this.b
                } //并且这个结果还会被缓存，但我们再次调用这个结果的时候并不会再次重新计算，而是单纯的重新调用，除非结果发生了变化。
            },

        })
    </script>
</body>
```


### 总结
在vue中虽然也可以使用事件来监听数据的改变，但是在大部分情况下使用vue中的 watch 和 computed 会更加的适合vue并且有更高的效率。在合适的场景正确的使用这两个选项可以很大程度上的提高我们的开发效率，要合理使用。
