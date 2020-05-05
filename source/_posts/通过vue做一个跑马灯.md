---
title: 通过vue做一个简单的跑马灯
date: 2019-06-15 22:38:59
tags:
 - JS
 - VUE
 - 动画
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前面简单的学习了vue中的一些简单的东西以及入门，这次通过vue来做一个简单的跑马灯来试一试.
<!-- more -->
### 实现原理
其实实现原理非常的简单，只需要将我们需要跑起来的字符串的首个字符拼接到字符串尾部，再通过定时器将新的字符串渲染到页面就可以了，只不过这些都是通过vue框架来实现的。

### 代码
```html
<body>
    <div v-cloak id="app">
        <button @click='pmd'>跑起来</button>
        <button @click='stoppmd'>停止</button>
        <p v-text="msg">  </p>
    </div>

    <script src="./../../js/vue.js"></script>
    <script>
    var vm=new Vue({
        el:'#app',
        data:{// 通过在data中设置变量      
            msg:'这是一个测试内容',
            timer:null,//通过timer这个变量来控制只有第一次单击有效
        },
        methods: {
           pmd(){
               if(this.timer!=null)
                   return ;  //如果timer为null，那么就直接调出函数
               this.timer=setInterval(() => {
                   var start=this.msg.substr(0,1);
                   var end=this.msg.substr(1);
                   this.msg=end+start;
                //    console.log(this.msg);       
               }, 500);
           },
           stoppmd(){
            clearInterval(this.timer);
            this.timer=null;
           }
        },
    }) 
    </script>
</body>
```

### 学习到的一些知识以及遇到的一些问题
* 学习到的第一个就是箭头函数，通过使用es6中的箭头函数就可以解决this的问题，就不用重新去指定this

* 第二个就是return的使用，过去我只知道return只能够返回函数值，现在我知道了return不仅仅能够返回函数值，还能够提前跳出函数返回函数值，配合条件表达式就能很好的实现一些功能。

* 第三个就是可以设置一个变量将定时器函数传给这个变量，然后就可以通过比如清除函数来清空定时器之类的以及重新开启定时器。

### 遇到的一些问题
一个就是对象中的this的指向问题，如果是对象中定义的方法，就可以直接通过this拿到当前对象，但是如果在对象中的方法中调用了其他方法，并在这个方法中写函数的话，那么这个时候的this就指向调用的外部函数，如果要重新让this指向对象就需要在自己定义的方法中声明``this=this``，或者使用es6中的箭头函数。

### 总结
在这次的学习中学习到很多es6中的新知识，下次要系统的完整的去学习es6中的知识。