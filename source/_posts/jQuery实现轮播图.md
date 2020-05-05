---
title: jQuery实现轮播图
date: 2019-05-13 20:07:57
tags:
 - JS
 - jQuery
 - 动画
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最近开始尝试着自己写轮播图，知道轮播图的原理，通过js顶死器不断的重复的给要轮播的图片添加和删除样式，在css样式中通过display中的 none 和block 来切换显示的图片，再加上过渡效果即可实现好看的轮播图，我准备用递归函数写，但是却不知道在哪出现了问题，能够实现第一轮的正常轮播，但是后面的轮播却会出现问题，不断的检查问题，还是没有找出问题，最后放弃用递归的写法，再参考别人的代码，成功实现了轮播图功能。
<!-- more -->
**直接放上代码:**
* html部分：
```html
<div class="lunbo">
        <div class="imgs">
            <!-- 两个左右切换的按钮 -->
        <span class="left"><</span>
        <span class="right">></span>
        <!-- 需要轮播的六张图片 -->
        <ul>
                <li class="current"><img src="img/a.jpg" alt=""></li>
                <li><img src="img/b.jpg" alt=""></li>
                <li><img src="img/c.jpg" alt=""></li>
                <li><img src="img/d.jpg" alt=""></li>
                <li><img src="img/e.jpg" alt=""></li>
                <li><img src="img/f.jpg" alt=""></li>
            </ul>
        </div>
        <!-- 图片下面部分可以用来切换图片的圆点 -->
        <ul class="dot">
            <li class="current"></li>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
        </ul>
    </div>
```
html部分比较简单。

* css部分
```css
*{
    margin: 0;
    padding: 0;
    list-style: none;
    text-decoration: none;
}
.lunbo{
    width: 100%;
    height:400px;
    position: relative;
}
.lunbo img{
    width: 100%;
    height: 450px;
    position: absolute;
    top: 0px;
}
.left{
    position: absolute;
    display: inline-block;
    font-weight: bolder;
    z-index: 99;
    left: 0px;
    width: 50px;
    height: 100px;
    line-height: 100px;
    background: #ccc;;
    top: 135px;
    font-size: 60px;
    text-align: center;
    opacity: 0.3;
}
.right{
    position: absolute;
    display: inline-block;
    font-weight: bolder;
    z-index: 99;
    right: 0px;
    width: 50px;
    height: 100px;
    line-height: 100px;
    background: #ccc;;
    top: 135px;
    font-size: 60px;
    text-align: center;
    opacity: 0.3;
}
.imgs>ul li{
    /* 先设置所有的图片都不显示 */
    display: none;
}
.imgs .current{
    /* 让第一张图片显示 */
    display: block;
}
.dot .current{
    /* 通过给小圆点来添加current类来使其变为红点 */
    background:red;
}
.dot{
    z-index: 99;
    position: absolute;
    top:300px;
    /* 可以使所有的小圆点整体居中 */
    left: 50%;
    transform: translate(-50%, -50%);
}
.dot li{
    /* 小圆点的默认样式 */
    background: white;
    width: 15px;
    height: 15px;
    border-radius:50%; 
    display: inline-block;   
    margin-right: 20px;
}
.dot>li:hover{
    /* 使鼠标经过左右切换的按钮变为一只手 */
    cursor: pointer;
}
```
很正常的css样式设置，很多地方还不是特别的熟练，不是完美，代码还有很多可以优化的地方。
* js部分
```javascript
$(function () {
    var timer = null;
    var cur = 0;
    var len = $(".imgs>ul li").length;
    // 右切换
    $('.right').click(function chr() {
        cur = $('.dot>.current').index();
        if (cur == 5) {
            $(".imgs>ul li").eq(0).fadeIn(400).siblings("li").fadeOut(400);
            $(".dot>li").eq(0).addClass("current").siblings().removeClass("current");
        }
        $(".dot>li").eq(cur + 1).addClass("current").siblings().removeClass("current");
        $(".imgs>ul li").eq(cur + 1).fadeIn(400).siblings("li").fadeOut(400);
    });
    // 左切换
    $('.left').click(function chl() {
        cur = $('.dot>.current').index();
        if (cur == 0) {
            $(".imgs>ul li").eq(5).fadeIn(400).siblings("li").fadeOut(400);
            $(".dot>li").eq(5).addClass("current").siblings().removeClass("current");
        }
        $(".dot>li").eq(cur - 1).addClass("current").siblings().removeClass("current");
        $(".imgs>ul li").eq(cur - 1).fadeIn(400).siblings("li").fadeOut(400);
    });
    //鼠标滑过容器停止播放
    $(".imgs").hover(function () {
        clearInterval(timer);
    }, function () {
        showImg();
    });
    // 遍历所有圆点导航实现划过切换至对应的图片
    $(".dot>li").click(function () {
        clearInterval(timer);
        cur = $(this).index();
        $(this).addClass("current").siblings().removeClass("current");
        $(".imgs>ul li").eq(cur).fadeIn(400).siblings("li").fadeOut(400);
    });
    //定义图片切换函数
    function showImg() {
        timer = setInterval(function () {
            cur++;
            if (cur >= len) {
                cur = 0;
            }
            $(".imgs>ul li").eq(cur).fadeIn(400).siblings("li").fadeOut(400);
            $(".dot>li").eq(cur).addClass("current").siblings().removeClass("current");
        }, 2000);
    }
    showImg();
});
```
* 最后整体思路没有变，但图片的切换就没有通过切换类来实现，而是使用了更加方便的jQuery的fadeIn()方法和fadeOut()，可以直接实现淡入淡出效果。
* 小圆点切换的实现还是通过切换类的方法
* 鼠标移到图片上自动停止播放则是通过jQuery的hover方法来实现的，移入的时候清除定时器，移出的时候再次执行切换函数的图片
* 点击小圆点切换图片这是通过，获取点击的那个小圆点的索引来定向切换图片
* 左右切换是获取当前小圆点的索引来定位，然后单击切换到下一张图片
写这个花了很多时间，不过最后还是完成了，也学会了很多其他的东西。

*最后是效果图*
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/gzDnChJBBj1zWVbIW2LTrUVXeKgmHGdz074rBbabFqU!/b/dLYAAAAAAAAA&bo=ewdXAgAAAAADBws!&rf=viewer_4">
