---
title: vue.js的学习（五）v-for 和key
date: 2019-06-16 15:39:18
tags:
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这次学习一下vue中的循环v-for和key,以及通过他们来遍历数组以及遍历对象并且将他们渲染到页面中.
<!-- more -->
### v-for的使用

```html
<body>
<div id="app">
    <!-- 遍历数组 -->
    <p v-for="(item, index) in arr">
            数组中的值是:{{item}}---索引是:{{index}}
    </p>
    <br>
    <!-- 遍历对象 -->
    <p v-for="(item, key, index) in obj ">
        {{key}}---{{item}}---索引是:{{index}}
    </p>
</div>

    <script src="./../../js/vue.js"></script>
    <script>
    var vm = new Vue({
        el:'#app',
        data() {
            return {
                arr:[1,2,3,4,5,6],
                obj:{
                    name:'zs',
                    age:18,
                    gender:'男'
                }            
            }
        },
    })
      
    </script>
</body>
```

* 可以看出v-for的使用非常的简单

* 在遍历数组的时候传递两个参数，那么第一个参数就是数组里面的值，第二个参数就是索引

```
<p v-for="(item, index) in arr">
            数组中的值是:{{item}}---索引是:{{index}}
    </p>
```

* 遍历对象的时候，传递两个参数，那么第一个参数是对象键值对中的值，第二个参数是对象中的键，如果有第三个参数那么第三个参数就是索引

```
<p v-for="(item, key, index) in obj ">
        {{key}}---{{item}}---索引是:{{index}}
</p>
```

### key的使用
在遍历的时候可以不加``key``
```
    <p v-for="(item, index) in items"></p>
```

也可以加``key``
```
    <p v-for="(item, index) in items" :key="index"></p>
```

那么二者之间有什么区别呢，在我上面的例子中没有什么区别，

**具体的区别可以参考 <a href="https://www.jianshu.com/p/4bd5e745ce95">这个帖子</a> 。解释的十分形象**

>注意：key最好是唯一标识符
```
<p v-for="(item, index) in items" :key="index"></p>
```
并且最好不要像这样设置，因为在排序的时候索引也是跟着一起在变化的，那像这样设置了基本上也等于没设置。