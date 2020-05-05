---
title: jQuery中eq与get的区别
date: 2019-05-20 19:57:14
tags:
 - JS
 - jQuery
categories:

---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在刚开始学习使用jQuery的时候就遇到过类似的问题，没有完全理解到什么是jQuery对象以它与Element对象的区别。而jQuery中的eq方法与get方法正好也是这方面的区别，这次来好好把他们区分一下。
<!-- more -->
先上一段html代码
```html
<body>
    <div class="test1">这是一个测试div <span>这是一个子元素</span></div>
    <div class="test2">这是第二个测试div</div>
</body>
```
js代码
```javascript
<script>
        $(function(){
            // 通过这个可以看出使用核心函数选择div会将它转化成一个jQuery对象，jQuery对象的本质是一个数组对象，有length属性
             console.log(
             $('div')
             );
            //  而我们通过get方法获取到的对象其实是一个Element对象
             console.log(
             $('div').get(0)
             );
            //  所以我们就可以对它使用js原生的方法来获取它的子节点
            console.log(
             $('div').get(0).children[0]
             );
            //  而这种写法获取到的东西是和get所获取到的东西是一模一样的
             console.log(
             $('div')[1]
             );
            //  而通过eq获取到的其实是一个jQuery对象
             console.log(
                 $('div').eq(0)
             );
            //  我们亦可以通过jQuery的核心函数将Element对象转化成jQuery对象
             console.log(
                $($('div').get(0))
             );            
        })   
    </script>
```
我们再来看看输出的结果:
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/s838PLLQqQDO8AAtICFTR2AbGzJ*Q9WLnphZ0VhyC4k!/b/dLYAAAAAAAAA&bo=UwdMAwAAAAADBzk!&rf=viewer_4">

### 总结:
eq与get的区别十分的明显,一个获取到的是jQuery对象,一个获取到的是Element对象,而我们的jQuery的方法只能对jQuery对象使用,所以我们要特别注意我们获取到的是什么对象,当然我们也可以通过jQuery核心函数将Element对象转化为jQuery对象.