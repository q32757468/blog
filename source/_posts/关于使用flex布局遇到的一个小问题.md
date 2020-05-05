---
title: 关于使用flex布局遇到的一个小问题
date: 2019-05-23 18:52:16
tags:
 - CSS
 - flex
categories:
 - 问题解决
 - CSS
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;昨天在给我的课程设计的网站做底部信息的时候遇到了一个问题，就是我给一个子元素div设置了固定的宽度，但是当我页面宽度发生变化的时候，这个div的宽度也会发生变化，毕竟我设置的是固定的宽度，所以这肯定不是我想要的效果，今天和昨天经过测试，现在终于把问题解决了，问题是由于flex引起的。
<!-- more -->
让我们先看我的一个测试的代码：
```html
<style>
.big{
    width: 100%;
    height: 500px;
    background-color: blue;
    display: flex;
    border: 2px solid #000;
    justify-content: center;
    align-items: center;
    margin: 0 auto;
}
.sm{
    width: 1200px;//这里是一个固定的宽度
    height: 400px;
    background-color: red;
    text-align: center;
    border: 3px solid yellow;//加了一个黄色边框，方便观察
}
</style>
<body>
    <div class="big">
        <div class="sm">这是测试的内容</div>
    </div>
</body>
```
按照我一开始的想法，既然设置了固定的宽度，应该就不会再发生改变了，即使是flex也不能改变它
可是我们再看看效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/2ZasPfoD4Z310yCmjZUQP0cMgeDC6y.Fo6n5a.5emN8!/b/dFMBAAAAAAAA&bo=cwKaAwAAAAADB8o!&rf=viewer_4">
很明显，里面的子元素的宽度也因为父元素的宽度变小了而变小了。

我的第一次尝试是给它加一个``min-width=1200px``，我们再看看效果：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/2ZasPfoD4Z310yCmjZUQP0cMgeDC6y.Fo6n5a.5emN8!/b/dFMBAAAAAAAA&bo=cwKaAwAAAAADB8o!&rf=viewer_4">
**还是一样，没有发生任何变化。那究竟要怎么解决这个问题呢，经过我的各种尝试和猜想，最终还是把问题解决了：**
那么要如何解决这个问题呢，看我解决后的代码：
```
<style>
.big{
    width: 100%;
    height: 500px;
    background-color: blue;
    display: flex;
    border: 2px solid #000;
    /* justify-content: center; */  //1、首先就是不能用这个属性
    align-items: center;
    margin: 0 auto;
}
.sm{
    width: 1200px;
    min-width: 1200px; //3、同时要加上最小宽度
    height: 400px;
    background-color: red;
    text-align: center;
    margin: 0 auto;    //2、只能使用这个来使子元素居中
    border: 3px solid yellow;
}
</style>
<body>
    <div class="big">
        <div class="sm">21413423</div>
    </div>
</body>
```
我们再来看看解决后的效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/mxkasvESB5M2s1EObpBHaY2h9PTMMEAIeIHgeyPzb50!/b/dDABAAAAAAAA&bo=TAOaAwAAAAADB*Q!&rf=viewer_4">
这次我们就可以看到，里面的内容没有被缩小了，问题终于得到了解决。

**其实还有一种办法可以避免这种问题，就是最外层子元素不使用flex布局，或者是不设置百分比的宽度，这样也不会出现这种问题，但是在有些情况下使用flex明显要方便很多，看实际情况选择哪种处理办法吧。**

**更新补充**
后来我又想到了一个相对较好的解决办法，那就使最外层的div可以设置为百分比的宽度，但是不设置flex，我们对要布局的子div给它再加一个父元素，让它变成孙子，我们给他加的这个div让他成为父元素，并且给这个父元素设置flex和固定的我们想要的宽度，这个时候通过父元素的flex给孙子元素flex布局就也不会出现这种问题了，这也是一种不错的解决办法（**推荐**）
### 总结
flex虽然好用，很多方面用起来很方便，但是也存在很多问题我还不知道，所以使用的时候要提前做好规划，避免出现问题，我的轮播图bug就是由于flex布局引起的，但是不知道是不是这个原因。
