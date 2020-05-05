---
title: flex使用的小demo
date: 2019-05-18 10:20:25
tags:
 - flex
 - CSS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前面学习了flex的相关知识，现在就这些知识运用到实例，通过写一个导航栏和一个搜索框试试效果怎么样。
<!-- more -->
### 通过flex布局一个等分的导航栏
代码：
```html
<style>
*{
    margin: 0;
    padding: 0;
    list-style: none;
}
nav{
    width: 40%;
    height: 50px;
    background: #ccc;
    margin: 0 auto;
}
nav>ul{
    display: flex;
    height: 100%;
    width: 100%;
    flex-flow: row nowrap;
    /* justify-content: space-around; */
    align-items: center;
}
/* 通过给子元素使用flex-grow会影响到父元素设置的justify-content，但是无论使用哪种方法都能达到相同的效果 */
ul>li{
    text-align: center;
}
/* 以下代码其实都可以不用，只通过一个justify-content: space-between
就能达到这种效果 */
ul>li:nth-child(1){
    flex: 0 0 auto;
    text-align: center;
}
ul>li:nth-child(2){
    flex: auto;
    text-align: center;
}
ul>li:nth-child(3){
    flex: auto;
    text-align: center;
}
ul>li:nth-child(4){
    flex: auto;
    flex: 0 0 auto;

}
</style>
<body>
    <nav>
        <ul>
            <li>栏目一</li>
            <li>栏目二</li>
            <li>栏目三</li>
            <li>栏目四</li>
        </ul>
    </nav>
</body>
```
效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/9aN0kH42p89ZshFZG445MMLUZbbKvh2kT0Mrnf*1Ic0!/b/dL8AAAAAAAAA&bo=gAcSBAAAAAADB7M!&rf=viewer_4">

### 通过flex布局一个搜索框
代码：
```html
<style>
*{
    margin: 0;
    padding: 0;
    list-style: none;
}
.search{
    margin: 0 auto;
    margin-top: 20px;
    width: 500px;
    display: flex;
}
input{
    /* 因为默认另一个button的flex-grow为0，
    只要设置这个输入框的flex-grow为1则可以占满剩下的宽度 */
    flex-grow: 1;
}
button{
    
    border: 0;
    background-color: red;
    outline: none;
    height: 30px;
    /* 当我们把button的高度调高之后，可以发现输入框的高度也一起随之升高了
    如果想要改变这种效果可以对输入框设置align-self
    */
}
</style>
<body>
    <div class="search">
        <input type="text">
        <button>点击搜索</button>
    </div>
</body>
```
效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/3.gm02VccVxwuCAERv6LYnJUGtKjFXsv2EDCsdffn14!/b/dLYAAAAAAAAA&bo=gAcSBAAAAAADB7M!&rf=viewer_4">

### 总结
使用flex布局真的好轻松，好方便啊，再也不用为各种繁琐的事情改开改去了。

