---
title: 关于float与inlie-block的使用
date: 2019-05-13 18:39:25
tags:
 - CSS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;虽然之前有使用过float来把元素进行布局，但是发现这种方法存在缺点，后面发现还有display:inlie-block也能用来布局，但是在实际使用过后同样也发现一些问题，这次来讲一下这两种方式存在的问题以及解决办法。
<!-- more -->

### 关于float的使用
#### 使用float时存在的问题
<img  src="http://m.qpic.cn/psb?/V131x4904WMIoW/hFu3dMSC9pgEHXrl8dTPfEbvDBq24XikuyEAK4UQtl0!/b/dMAAAAAAAAAA&bo=gAcSBAAAAAADB7M!&rf=viewer_4">

可以看出当我们对子元素的div使用了float的时候，子元素成功浮动在了一行，但是父元素却出现了问题，父元素的宽度并没有被撑起，而是高度塌陷了，成为了一条线，这是因为使用了float之后会使文档脱离标准流，从而没有元素撑起父元素的高度。那么为了解决这个问题也有许多办法。
#### 解决办法
##### 给父级元素设置一个固定的高度
```css
.father{
    height: 400px;
}
```
缺点：不够灵活
##### 也给父级加上浮动
```css
.father{
    float: left;
}
```
缺点：会使父级与其他元素的关系发生变化，不推荐使用
##### 给父级使用overflow  + zoom
```css
.father{
    overflow: hidden;
    zoom:1; /* 兼容处理,触发IE6/7的haslayout */
}
```
缺点：如果使用了定位，或者设置了边距的话，超出了父级的话将被隐藏
但是使用起来比较方便，副作用也比较小比较好控制，推荐使用

##### 在父级中加上一个div
* 兼容性较强
* 但是代码较多，且不利于维护优化

##### 使用after伪元素
```css
.father:after{
    content: ".";         /*生成内容作为最后一个元素，至于content里面是什么没有影响*/
    display: block;       /*使得生成的元素以块级元素显示，占满剩余空间*/
    height: 0;            /*避免生成的内容破坏原有空间的高度*/
    clear: both;          /*闭合浮动*/
    visibility: hidden;   /*使得生成内容不可见，并允许可能生成内容盖住的内容进行点击和交互*/
}
```
除了代码较多不好记之外是比较完美的解决方案，推荐使用。

---
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/h*RnPv4cAn236H0pKOimXF0K5BtXCd*BWO0fHY.iz2M!/b/dL8AAAAAAAAA&bo=gAcSBAAAAAADF6M!&rf=viewer_4" >
使用了以上的办法，可以发现问题都能成功的解决。

### 关于display:inlie-block的使用
#### 使用display:inlie-block时存在的问题
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/xyPesPY7PBgaYF0GoJVet7en.nQOe.ffy6QfzKyNzhM!/b/dLYAAAAAAAAA&bo=gAcSBAAAAAADB7M!&rf=viewer_4">
可以看出在使用display:inlie-block之后虽然子元素能够横向排列了，但是他们之间却会出现间隙，不过这种间隙不是来自边距，而是因为我们在书写div时回车会产生回车符，回车符相当于空白符，通常情况下，多个连续的空白符会合并成一个空白符，而产生“空白间隙”的真正原因就是这个让我们并不怎么注意的空白符。

#### 解决办法
我们可以发现其实这个空白符的间距其实是和字体大小有关系的，所以我们只需要将父级的字体大小设为0即可消去空白符。
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/n7IFt0AsI0NUFgDZWjetPeLuM4wGFTcOtI8rODlu76w!/b/dLYAAAAAAAAA&bo=gAcSBAAAAAADB7M!&rf=viewer_4">
我们可以看到空白符已经被消去了，但是里面的字也不见了，所以我们要记得给子元素设置字体大小。

#### 后面又发现的问题
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/5GwG*x0yPGXDrqTdn7wYqsS.MUQWKTvx2MfbVl42CP8!/b/dL4AAAAAAAAA&bo=gAcSBAAAAAADB7M!&rf=viewer_4">
* 通过图片我们可以看出，第一个和第三个子元素没有内容，那么他们就不会和有内容的子元素同占一行，会发生错位
* 而将他们同时设为有内容或者没有内容的时候这种情况就消失了，所以要十分注意这个问题
