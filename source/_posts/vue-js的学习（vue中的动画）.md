---
title: vue.js的学习（vue中的动画）
date: 2019-06-20 20:28:09
tags:
 - 动画
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一开始在使用vue中的动画的时候我没有太明白究竟是个怎么样的机制，以及如何高度的定义一些动画，经过我的尝试，现在终于算是弄明白时怎么一回事了<a href="https://cn.vuejs.org/v2/guide/transitions.html#CSS-%E5%8A%A8%E7%94%BB">这里是官方文档</a>。
<!-- more -->
### vue中动画的特点
vue中的动画主要分为两部分，一是进场动画，二是离场动画，vue中的动画主要是监听了设置动画的元素的显示和隐藏，当元素在页面中被显示的时候就会执行进场动画，当元素被隐藏的时候就会执行离场动画，我们可以通过设置两组类来设置离场动画和进场动画。

**一个简单的小例子**
```html
<style>
    /* 这两个类一个是动画的起始位置,一个是动画结束的时候的位置 */
    .dh1-enter,
    .dh1-leave-to {
        transform: translateX(40px);
        opacity: 0;
    }  
    
      /* 这两个类一个是进入时候的动画效果，一个是离开时候的动画效果 */
    .dh1-enter-active,
    .dh1-leave-active {
        transition: all 1s ease;

    }

</style>

<body>
    <!-- vue中的动画可以分为两部分，一部分是进入的动画，一部分是离开的动画，当元素被隐藏的时候会触发离开的动画，出现的时候会触发进入的动画 -->

    <div id="app">
        <!-- 这是通过这种transition和两个种类来设置动画 -->
        <button @click="flag=!flag">点击切换状态</button>
        <button @click="flag2=!flag2">点击切换状态2</button>
        <!-- 通过添加appear 可以使动画在渲染到页面的时候就执行一次 -->
        <transition name="dh1" appear>
            <p v-show="flag">这是一段话第一段话</p>
        </transition>
        <transition name="dh2">
            <p v-show="flag2" :class="{animated:true,bounce:true}">这是一段话第二段话</p>
        </transition>
    </div>

    <script src="./../../js/vue.js"></script>
    <script>
        var vm = new Vue({
            el: '#app',
            data() {
                return {
                    flag: 'true',
                    flag2: 'false',
                }
            },
            methods: {

            },
        })
    </script>
</body>
```

### 单元素过渡
+ 可以看出其实vue中将动画主要分成了两部分
 - 一部分是入场时的动画，也就是动画从隐藏到显示的这个过程中所执行的动画
 - 另一部分是离场动画也就是元素从显示到隐藏这个过程中所执行的动画

+ 可以通过4个类来分别控制动画，就像上面的例子中的那样

+ 也可以通过vue来添加类来引用动画库中的动画

### 钩子函数
vue中的钩子函数将进场动画和离场动画进行了细分，包括进场前和执行动画时，以及动画结束后，通过设置钩子函数就可以对动画的各个时机进行详细的设置。
可以通过钩子函数来设置只进行进场动画或者离场动画，以及对外部引用的动画进行很好的控制。

可以通过这个例子看出来：
```html
<style>
    .ball {
        width: 30px;
        height: 30px;
        background-color: bisque;
        border-radius: 50%;
        margin-top: 10px;
    }
</style>

<body>
    <!-- 通过钩子函数来实现动画就不再是通过设置样式来实现动画效果，而是通过js来设置样式来实现动画效果 -->
    <!-- 也是分为几个阶段，主要是两部分，一是进场动画，一是离场动画 -->
    <!-- 进场动画又分为 -->
    <!-- 进场前：主要是通过设置style来设置起始时的样式位置之类的 -->
    <!-- 进场时：主要通过设置style来设置动画结束时的位置，以及动画属性的设置如动画时常 -->
    <!-- 进场后：动画完成后所执行的东西 -->

    <!-- 其实钩子函数的原理还是对单元素的过渡做了封装，只不过是可以通过js来控制只进行进场动画或者只进行离场动画 -->
    <div id="app">
        <button @click="flag=!flag">单击</button>
        <transition v-on:before-enter="beforeEnter" v-on:enter="enter" v-on:after-enter="afterEnter">
            <div v-show="flag" class="ball"></div>
        </transition>

    </div>

    <script src="./../../js/vue.js"></script>
    <script>
        var vm = new Vue({
            el: '#app',
            data() {
                return {
                    flag: false,
                }
            },
            methods: {
                // 所有的钩子函数都必须传一个el参数，表示操作的原生dom对象
                beforeEnter(el) {
                    el.style.transform = "translate(0,0)";
                },

                enter(el, done) { //在单纯的使用js钩子函数设置动画时必须传一个done参数来执行完成后的操作，这个done实际上是after-enter所绑定的方法
                    el.offsetWidth; //这个必须加，不加出bug，没有为什么
                    el.style.transform = "translate(40px,100px)";
                    el.style.transition = "all 1s ease";
                    done();
                },
                afterEnter(el) {
                    // 这里因为只需要重复这个半场动画，这个从隐藏到出现从上到下的过程，所以将元素设置为不显示，下次再单击的时候就又是执行的是从上到下的动画了
                    this.flag = !this.flag;
                }
            },
        })
    </script>
</body>
```

### 列表动画
在前面的所有例子都是设置的单元素动画，不能用于设置列表中的动画，但我们需要设置一个列表动画，让其中的所有元素都有动画效果的时候，我们就可以使用``transition-group``这个标签来包裹我们想要设置动画的列表。
就像下面这个例子这样：
```html
<style>
    span {
        margin: 5px;
        /* 这里要注意一件事情，必须设置为inline-block,不然元素不会从下面出来，或者使用弹性布局也可以 */
        display: inline-block;
    }

    .v-enter-active,
    .v-leave-active {
        opacity: 1;
        transition: all 1s ease;
    }

    .v-enter,
    .v-leave-to {
        transform: translateY(30px);
        opacity: 0;
    }

    /* 要实现其他元素位移时有动画必须添加下面这个类，并且设置动画 */
    .v-move {
        transition: all 1s ease;
    }

    /* 而且还必须单独给v-leave-active这个类设置绝对定位才可以实现效果 */
    .v-leave-active {
        position: absolute;
    }
</style>

<body>
    <div id="app">
        <button style="margin-left: 10px" @click="addnum">add</button>
        <button style="margin-left: 10px" @click="delnum">del</button>
        <transition-group tag="div" :style="{margin:'10px'}">
            <!-- 如果要让原来元素再插入的过程中保持位置不变，就需要设置key -->
            <span v-for="(item,index) in list" :key="item">{{item}}</span>
        </transition-group>
    </div>

    <script src="./../../js/vue.js"></script>
    <script>
        var vm = new Vue({
            el: "#app",
            data() {
                return {
                    list: [1, 2, 3, 4, 5, 6, 7, 8, 9],
                    num: 10,
                }
            },
            methods: {
                randomIndex() {
                    // Math.floor()的作用是返回一个小于等于参数的整数
                    return Math.floor(Math.random() * this.list.length)
                },
                addnum() {
                    // splice方法还可以用来在指定位置添加元素，第三个参数就是要添加的元素
                    this.list.splice(this.randomIndex(), 0, this.num++)
                },
                delnum() {
                    this.list.splice(this.randomIndex(), 1)
                }
            },
        })
    </script>
</body>
```

+ 这里要注意的几件事是：
 - 如果要使动画生效，必须将要设置动画的元素设置为块级元素或者行内块级元素，如果是行内元素则不会有动画效果。
 - 如果要使其他元素在发生位置移动也拥有动画过渡的效果这需要添加一个``v-move``这个类，当然，这个名称也是一样可以修改的，然后在这个类中设置动画过渡效果，像上面的这个例子这样。
 - 但是其实只``v-move``这个类其实是还不够的，还需要在``v-leave-active``中设置绝对定位，这里要注意的一件事情是，这里必须单独只给这一个类设置，否则也不会有动画效果，一开始的时候我贪图方便就直接在设置active这组动画的时候设置上绝对定位，也就是给两个类都加上了绝对定位，结果发现这样并没有效果，只有单独给一个类设置的时候才有效果。

### 追加，使用第三方库的动画
在最刚开始的时候我认为在vue中给元素添加动画必须要用到vue中提供的控制动画的那几个类或者是钩子函数来实现动画，因为刚接触vue，不是特别熟悉，所以我认为如果要使用第三方的库来给元素添加动画的话应该是通过通过钩子函数来直接控制，但是今天看到了一个别人的用法，有一种恍然大悟的感觉，其实可以直接通过下面的这种方式来设置：
```html
<body>
    <div id="app">
        <button @click="flag=!flag">单击切换</button>
        <transition appear :duration="{ enter: 2000, leave: 1000 }" enter-active-class="bounceInLeft"
            leave-active-class="bounceOutRight">
            <p class="animated " v-show="flag">这是一段测试文字</p>
        </transition>
    </div>

    <script src="./../../js/vue.js"></script>
    <script>
        var vm = new Vue({
            el: "#app",
            data() {
                return {
                    flag: true,
                }
            },
            methods: {

            },
        })
    </script>
</body>
```
>直接通过设置两个``active``类就可以实现动画的效果了，一个是进场动画，一个是离场动画，可以通过``:duration="2000"``来设置动画的时间，可以像这样直接只传一个时间来设置两个动画的时间，也可以像上面的例子一样通过一个对象来设置两个动画的不同时间。


而在此之前我都是想通过钩子函数来给元素添加类来实现动画的效果，很显然不如上面的这种方式方便，当时在遇到比较复杂的情况的时候还是要使用钩子函数。不得不说vue真是一款很好的框架！

### 总结
要熟练设置vue中的动画就必须要理解vue中动画的运作机制，理解进场和离场这两个过程，然后可以通过设置两组类来设置动画的效果，当这种方法无法满足需求的时候就可以通过钩子函数来满足我们的需求。