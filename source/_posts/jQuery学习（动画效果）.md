---
title: jQuery学习（动画效果）
date: 2019-05-15 19:30:50
tags:
 - JS
 - jQuery
 - 动画
categories:
 - 学习笔记
 - jQuery学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这次总结以下jQuery中所有自带的动画效果以及jQuery中的自定义动画，以及动画1的其他一些相关方法，帮助我们更好的学习和使用jQuery中的动画。前面已经学习使用过大部分自带动画了，这次着重讲一下自定义动画与其他方法。
<!-- more -->
### 显示、隐藏动画
show([s,[e],[fn]])
* 显示动画
* 内部实现原理根据当前操作的元素是块级还是行内决定, 块级内部调用display:block;,行内内部调用display:inline;
---
 hide([s,[e],[fn]])
* 隐藏动画
---
toggle([spe],[eas],[fn])
* 切换动画(显示变隐藏,隐藏变显示)
---
注意事项:

* show(1000, function () {};) 第一个参数单位是毫秒, 1000毫秒等于1秒
* 默认的动画时长是400毫秒
* 除了指定毫秒以外还可以指定三个预设参数 slow、normal、fast
* slow本质是600毫秒
* normal本质是400毫秒
* fast本质是200毫秒

### 展开、收起动画
参数、注意事项和显示隐藏动画一模一样, 只不过动画效果不一样而已
* slideDown([s],[e],[fn])
展开动画
* slideUp([s,[e],[fn]])
收起动画
* slideToggle([s],[e],[fn])
切换动画(展开变收起,收起变展开)
参数、注意事项和显示隐藏动画一模一样, 只不过动画效果不一样而已
* fadeIn([s],[e],[fn])
淡入动画
* fadeOut([s],[e],[fn])
淡出动画
* fadeToggle([s,[e],[fn]])
切换动画(显示变淡出,不显示变淡入)
* fadeTo([[s],o,[e],[fn]])
淡入到指定透明度动画
可以通过第二个参数,淡入到指定的透明度(取值范围0~1)

### 自定义动画
有时候自带的这些动画不能完全满足我们的需求，我们就需要使用到自定义动画来帮助我们完成动画。

animate(p,[s],[e],[fn])
* 第一个参数: 接收一个对象, 可以在对象中修改属性
* 第二个参数: 指定动画时长
* 第三个参数: 指定动画节奏, 默认就是swing
* 第四个参数: 动画执行完毕之后的回调函数
下面是一个自定义动画的例子：
```html
<style>
.test{
    background: red;
    width: 200px;
    height: 200px;
    border-radius: 50%;
}
</style>
<body>
    <div class="test"></div>
    <script>
    $(function(){
        // 使圆变大
        $('.test').animate({
            // 可以同时设置多个值的变化
            width:400,
            height:400,
            // 可以设置动画节奏
        },2000,'Linear',function(){
            // 可以链式设置多个动画
            alert('变大执行完毕')
        }).animate({
            marginLeft:200
        },2000,function(){
            alert('右移执行完毕')
        }).animate({
            marginLeft:"800"
        },2000,function(){
            alert('完成')
        });
    })
    </script>
</body>
```
要注意的一点:
每次开始运动都必须是初始位置或者初始状态,如果想在上一次位置或者状态下再次进行动画可以使用累加动画,上面这个例子就是圆先变大,然后在变大之前的圆心处右移,然后再回到圆心处再次右移,所以要使用累计动画的方式来设置参数比如说``{marginLeft:"+=200"}``

### 动画队列
* 多个动画方法链式编程,会等到前面的动画执行完毕再依次执行后续动画
* 但是如果后面紧跟一个非动画方法则会被立即执行
* 如果想颜色再动画执行完毕之后设置, 1.使用回调 2.使用动画队列
* 注意点:
动画队列方法queue()后面不能继续直接添加queue()
如果想继续添加必须在上一个queue()方法中next()方法

### 动画相关方法
* delay(d,[q])
设置动画延迟时长
* stop([c],[j])
停止指定元素上正在执行的动画
```
// 立即停止当前动画, 继续执行后续的动画
// $("div").stop();
// $("div").stop(false);
// $("div").stop(false, false);

// 立即停止当前和后续所有的动画
// $("div").stop(true);
// $("div").stop(true, false);

// 立即完成当前的, 继续执行后续动画
// $("div").stop(false, true);

// 立即完成当前的, 并且停止后续所有的
$("div").stop(true, true);
```