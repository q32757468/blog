---
title: Bootstrap的学习与使用
date: 2019-06-13 20:15:34
tags:
 - CSS
 - Bootstrap
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其实学习bootstrap是在写这篇博客很久之前的事情了，但是由于一些其他的很多事情，就没有第一时间写这篇博客，记录关于bootstrap中学习的一些事情。现在来写一下。bootstrap作为现在非常流行的响应式框架，必然是要学习的，起初我对bootstrap的了解并不是很多，只知道这算是一个框架，然后我就在网上看视频跟着学，然后等学习完了就清楚了，bootstrap其实是一个前端的css库，通过调用各种css演示可以很轻松的将一个响应式的网站给搭建起来，其实最另我没有想到的是使用bootstrap框架开发竟然如此便捷，甚至可以几乎不用自己写一点的css演示就可以有一个漂亮美观的页面，其实bootstrap要学的东西不多，大部分在<a href="https://www.bootcss.com/">文档</a>中找到相应的使用方法就会用了,这篇博客记录一下我在学习之初所做的一些笔记。
<!-- more -->


### 字体图标的使用
* bootstrap中有许多的字体图标，但是数量也是有限的，所以我们需要一些自定义的字体图标，图标网站有很多，我比较喜欢去<a href="https://www.iconfont.cn">阿里矢量图标库找</a>
* 参考阿里矢量图标库给的使用文档所提供的三种使用图标的方法：

#### 第一种：Unicode 引用

Unicode 是字体在网页端最原始的应用方式，特点是：

* 兼容性最好，支持 IE6+，及所有现代浏览器。
* 支持按字体的方式去动态调整图标大小，颜色等等。
* 但是因为是字体，所以不支持多色。只能使用平台里单色的图标，就算项目里有多色图标也会自动去色。
>注意：新版 iconfont 支持多色图标，这些多色图标在 Unicode 模式下将不能使用，如果有需求建议使用symbol 的引用方式

Unicode 使用步骤如下：

第一步：拷贝项目下面生成的 @font-face
```css
@font-face {
  font-family: 'iconfont';
  src: url('iconfont.eot');
  src: url('iconfont.eot?#iefix') format('embedded-opentype'),
      url('iconfont.woff2') format('woff2'),
      url('iconfont.woff') format('woff'),
      url('iconfont.ttf') format('truetype'),
      url('iconfont.svg#iconfont') format('svg');
}
```
第二步：定义使用 iconfont 的样式
```css
.iconfont {
  font-family: "iconfont" !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```
第三步：挑选相应图标并获取字体编码，应用于页面
```html
<span class="iconfont">&#x33;</span>
```
>"iconfont" 是你项目下的 font-family。可以通过编辑项目查看，默认是 "iconfont"。

#### 第二种：font-class 引用
font-class 是 Unicode 使用方式的一种变种，主要是解决 Unicode 书写不直观，语意不明确的问题。

与 Unicode 使用方式相比，具有如下特点：

* 兼容性良好，支持 IE8+，及所有现代浏览器。
* 相比于 Unicode 语意明确，书写更直观。可以很容易分辨这个 icon 是什么。
* 因为使用 class 来定义图标，所以当要替换图标时，只需要修改 class 里面的 Unicode 引用。
* 不过因为本质上还是使用的字体，所以多色图标还是不支持的。
使用步骤如下：

第一步：引入项目下面生成的 fontclass 代码：
```css
<link rel="stylesheet" href="./iconfont.css">
```
第二步：挑选相应图标并获取类名，应用于页面：
```html
<span class="iconfont icon-xxx"></span>
```
>" iconfont" 是你项目下的 font-family。可以通过编辑项目查看，默认是 "iconfont"。

#### 第三种：Symbol 引用

这是一种全新的使用方式，应该说这才是未来的主流，也是平台目前推荐的用法。相关介绍可以参考这篇文章 这种用法其实是做了一个 SVG 的集合，与另外两种相比具有如下特点：

* 支持多色图标了，不再受单色限制。
* 通过一些技巧，支持像字体那样，通过 font-size, color 来调整样式。
* 兼容性较差，支持 IE9+，及现代浏览器。
* 浏览器渲染 SVG 的性能一般，还不如 png。
使用步骤如下：

第一步：引入项目下面生成的 symbol 代码：
```html
<script src="./iconfont.js"></script>
```
第二步：加入通用 CSS 代码（引入一次就行）：
```css
<style>
.icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>
```
第三步：挑选相应图标并获取类名，应用于页面：
```
<svg class="icon" aria-hidden="true">
  <use xlink:href="#icon-xxx"></use>
</svg>
```
这里用第二种方法与第三种方法做一个演示：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/9VaJWjOEu1kDCMcQ6.weozBu0ZKwqIpi6.AZtmjQMZw!/b/dL4AAAAAAAAA&bo=LQF3AAAAAAADB3k!&rf=viewer_4">
可以看到无论用哪种方法都能成功使用到字体图标，既然symbol的方法使未来趋势那就尽量多使用symbol吧，毕竟这种方式还可以支持多色图标
>有一件事情要注意，就是在最开始的时候我通过symbol方式去设置图标的颜色发现并不成功，后面查阅资料后发现是因为当我在下载图标代码的时候这个图标本身使带有颜色的，这里有两种办法：
1. 打开js文件，将``fill="#......"``删除。
2. 到iconfont.cn上，全选项目中的文件，应用批量操作-->批量去色

当然啦，还有其他的特别多的图标库，像font awesome这种专门为bootstrap设计的之类的，看个人喜好用什么，这里就介绍了引用阿里矢量图标库中的图标



### 选项卡的适配
* 当页面的宽度缩小到一定的程度的时候，选项卡中的选项就会换行，有时这不是我们想要的效果，我们想要的效果是当所有选项的宽度之和大于外部的宽度的时候它就会出现一个横向的滚动条

* 那我们就可以通过``overfow ：auto``来实现，但是只设置``overflow``很显然是不能达到这种效果的，因为这个时候当我们把页面的宽度缩小选项卡的头部会进行换行

* 所以我们还需要使它不进行换行，因为b4相较于b3进行了重写，不再使用float布局而是使用flex布局，所以我们可以通过flex来进行设置不换行，先设置为flex布局，再设置为不换行，这时候就实现了我们想要的效果

```html
<style>
    a {
        padding-right: 100px !important;
    }
    .nav-tabs {
        /* width: 400px; */
        padding-bottom: 1px;
        margin-top: 0;
    }
</style>

<body>
    <div class="container">
        <ul class="nav nav-tabs d-flex overflow-auto flex-nowrap">
            <li class="nav-item">
                <a href="#" class="nav-link active">Active</a>
            </li>
            <li class="nav-item">
                <a href="#" class="nav-link">Link</a>
            </li>
            <li class="nav-item disabled">
                <a href="#" class="nav-link">Disabled</a>
            </li>
            <li class="nav-item">
                <a href="#" class="nav-link active">Active</a>
            </li>
        </ul>
    </div>
</body>
```
再来看一下实现的效果：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/Xs6hPal3O3QBEACXAhSkQsqvigd0aU4LQk*D937SLas!/b/dD4BAAAAAAAA&bo=twJvAAAAAAADB*g!&rf=viewer_4">
成功实现效果！


### 一些小笔记
* **bootstrap自适应的底层是用媒体查询做的**
通过媒体查询来定义不同分辨率下容器的不同宽度，以及栅栏系统的宽度。

* **bootstrap是组件化开发，有很多的组件可以使用，包括像各种定义好样式的按钮，各种按钮可以用<a href="http://blog.koalite.com/bbg/">bbg</a>来生成。**

* **bootstrap中的容器**
container与container-fluid的作用就是当成一个容器，这个容器自带了响应式的布局。这两个容器的区别就是一个是固定的宽度，一个是百分之百的宽度，container用的比较多.
container拥有15px的左右内边距，可以用row来抵消。

* **多套轮播图的处理**
在响应式的布局中可能会有两套轮播图，一套显示在pc端，一套显示在手机端，pc端的图片用背景图来处理，只显示中间的那一部分，只有在宽度在拉长的时候会把边上的纯色部分显示出来
处理方式是通过 background:no-repeat center center 来将背景图片居中
然后通过background-size:cover来让图片显示
HTML元素自定义属性的方法是通过data来设置，比如设置 data-name="hsl" 属性，
然后通过jQuery中的data("name")就可以拿到我们想要的数据，
然后通过 data("name","sss")就可以设置属性
然后就可以通过这种方法来给标签设置属性，再通过jQuery动态的设置图片

* **在bootstrap中有很多的全局样式，能用bootstrap自带的样式就用自带的（比如颜色）**

* **补充一个css样式的用法，设置背景颜色可以设置trans什么的来继承父级的颜色**

* **使用bootstrap的时候，在布局的时候主要是使用栅格系统，就可以很轻松的将一大块内容分割成我们想要的部分。**

* **bootstrap中的徽章可以用来做圆角边框并且自带颜色**

* **bootstrap中的媒体对象可以用来处理图片加文字的并排情况**

* **在容器栅格布局中尽量不要使用margin，而是使用padding，因为本身12份已经占满了，没有多余的空间来设置margin**

* **使用栅栏系统的时候当满了12列，剩下的就会换行，比如有4个col-md-6,那么这里就会占满两行。**

* **栅格系统中的内容居中的方法有很多，比如可以用``text-align``或者是flex布局，对于图片来说的话可以使用``margin auto`` 来设置要注意的一点是图片是一个行内元素，不能直接设置上下边距，如果要设置则需要将其设置为块级元素或者是行内块级元素**

* **如果要给整块内容添加超链接直接在内容最外层添加a标签即可**

* **在很多时候可以不用设置高度，而是可以直接通过设置padding值来让内容撑开**

* **在使用bootstrap进行响应式开发的时候一定要注意最外层应该不要设置固定高度，否则在自适应的时候可能会出现问题**

* **通过full-left可以将本来占一行的元素浮动到一行,b4中使用``offset-``来设置向左或是向右，通过设置display为inline-block也能实现这样的效果**


### 总结
学习总结是一个漫长的过程，这篇博客还会持续记录一些问题。