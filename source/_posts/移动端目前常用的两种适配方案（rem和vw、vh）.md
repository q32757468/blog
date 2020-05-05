---
title: 移动端目前常用的两种适配方案（rem和vw、vh）
date: 2019-11-23 10:22:42
tags:
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;PC端的web开发是比较简单的，不用做任何的适配就可以直接开发，但是手机端就不一样了，因为市场上充满着各种品牌型号的手机，所以会存在着许多种不一样的分辨率，那么如何对这么多的分辨率进行适配就是一个问题了，这篇文章主要介绍目前常见的两种移动端适配方案 rem 适配以及 css 新单位 vw、vh。
<!-- more -->
### rem适配
在css3 的新单位出现之前，移动端的适配主要都是通过 rem 适配来实现的，em 这个单位想必大家都很熟悉，就是相较于父级字体大小的一个单位，那么 rem 也是相似的， rem 中的 r 代表着 root ，也就意味着不再是相较于父级了 ，而是相较于根级了，也就是 html 了，所以我们才可以通过 rem 来进行移动端的适配。

我们只需要通过 js 来**动态的改变 html 的 ``font-size``**，就可以做到 rem 在不同的分辨率下也会有一样的表现。我们只需要将 html的 font-size 与屏幕的宽度关联起来就可以实现这样的效果，可以参考这段代码：
```js
    let htmlDom = document.getElementsByTagName('html')[0];
    window.addEventListener('resize', (e) => {
      //获取视窗宽度
      let htmlWidth = document.documentElement.clientWidth || document.body.clientWidth;
      //假如设计稿的宽度为750px则除7.5，以此类推
      htmlDom.style.fontSize = htmlWidth / 7.5 + 'px';//这样处理后就可以得到 1rem=设计稿物理像素100px=50px css像素
    })
```
这里是以设计稿的宽度为 750px(iphone6)为标准来书写的，那明明我们打开控制台看到的 iPhone6的宽度为 375，为什么设计稿是 750 呢，这是因为这个750 是物理像素，而我们看到的 375 则是 css像素，这也就意味着在 iPhone 上 1css像素=2物理像素。

```js
htmlDom.style.fontSize = htmlWidth / 7.5 + 'px';
```
通过这段代码我们就将屏幕的宽度与 font-size 结合在一起了，就可以达到 1rem 在不同屏幕上会有相同的表现了，有些人可能会奇怪为什么这里是 用获取到的宽度除以7.5，其实这个是没关系的，这个比例是多少都可以，只不过在设计稿的宽度为 750px的情况下 这样可以做到 1rem=100物理像素=50css像素，方便我们书写，假如设计稿上 body 的宽度为 750px ，我们只需要在 css 中写``body{width:7.5rem} ``，只需要将设计稿的宽度除以 100 就可以了。

具体可以参考这个演示：(以设计稿为 750px 为例)
```html
  <script>
    let htmlDom = document.getElementsByTagName('html')[0];
    window.addEventListener('resize', (e) => {
      //获取视窗宽度
      let htmlWidth = document.documentElement.clientWidth || document.body.clientWidth;
      //加入设计稿的宽度为750px则除7.5，以此类推
      htmlDom.style.fontSize = htmlWidth / 7.5 + 'px';//这样处理后就可以得到 1rem=设计稿物理像素100px=50px css像素
    })
  </script>
  <link rel="stylesheet" href="css/normalize.css">
</head>
<style>
  body {
    font-size: 16px;
  }

  .testBox {
    width: 100%;
    background-color: bisque;
    text-align: center;
  }

  .left {
    background-color: beige;
    width: 4.5rem;
    height: 10rem;
    float: left;
    text-align: center;
  }

  .right {
    background-color: aqua;
    width: 3rem;
    height: 10rem;
    float: right;
    text-align: center;
  }
</style>

<body>
  <div class="testBox">这是一个测试内容</div>
  <div class="left">这是左边的内容</div>
  <div class="right">这是右边的内容</div>
</body>
```
效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/WJKDSWkhwAIlDynoA55phRhEejem89Sb4C38cFr6UeA!/b/dL8AAAAAAAAA&bo=WgIvAwAAAAADB1Y!&rf=viewer_4">
iPhone6

<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/R525uo4TZaYDOdD6OtUAj2gAxd4fcnpWcQSscYW6D7I!/b/dL4AAAAAAAAA&bo=KQIkAwAAAAADFz4!&rf=viewer_4">
其他手机

可以看出效果良好。


###  vw和vh
虽然 rem 较好的解决了移动端适配的问题，但是却把 css 和 js 耦合在了一起，很显然这个做法并不完美，所以就有了 css3 的视口单位(Viewport units) vw 和vh。

#### 什么是视口？
在桌面端，指的是浏览器的可视区域；在移动端，它涉及3个视口：Layout Viewport（布局视口），Visual Viewport（视觉视口），Ideal Viewport（理想视口）。

视口单位中的“视口”，桌面端指的是浏览器的可视区域；移动端指的就是Viewport中的Layout Viewport。

根据CSS3规范，视口单位主要包括以下4个：
  + vw：1vw等于视口宽度的1%。
  + vh：1vh等于视口高度的1%。
  + vmin：选取vw和vh中最小的那个。(通常用来处理屏幕旋转)
  + vmax：选取vw和vh中最大的那个。(通常用来处理屏幕旋转)

其实到这里可以看出视口单位也是与百分比相似的单位，但是**vh/vw与%区别在于 % 相较的是祖先元素 ，而 vh/vw 相较的是 视口，**所以才可以用 vh/vw 来做移动端的适配。

可以配合 sass 函数来将 px 转化为 vh/vw。
```scss
//iPhone 6(750px)设计稿基准

$vm_base: 750; 

@function vm($px) {

    @return ($px / 375) * 100vw;

}
```
实现和上面一样的效果：
HTML:
```html
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0,user-scalable=no">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <link rel="stylesheet" href="css/normalize.css">
  <link rel="stylesheet" href="./css/vwvh.css">
</head>


<body>
  <div class="testBox">这是一个测试内容</div>
  <div class="left">这是左边的内容</div>
  <div class="right">这是右边的内容</div>
</body>
```

SCSS:
```scss
$vm_base: 750;

@function vm($px) {

  @return ($px / $vm_base) * 100vw;

}

body {
  font-size: 16px;

  .testBox {
    width: 100%;
    background-color: bisque;
    text-align: center;
  }

  .left {
    background-color: beige;
    width: vm(450);
    height: vm(1000);
    float: left;
    text-align: center;
  }

  .right {
    background-color: aqua;
    width: vm(300);
    height: vm(1000);
    float: right;
    text-align: center;
  }
}
```
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/kcrvt7i3H3X0KohfGDN7UUjcWDm1YiURlMimZK*RAXc!/b/dL8AAAAAAAAA&bo=6gFfAwAAAAADB5U!&rf=viewer_4">
iPhone6

<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/cU9.Q5dw8svkWgbv5fWUUjex91PWKBctmjoQF0zkaCs!/b/dD4BAAAAAAAA&bo=agKBAwAAAAADB8g!&rf=viewer_4">
其他手机

### 总结
这篇文章主要介绍了目前常见的两种移动端的适配方案，但是很明显，逐渐的vh/vw将会称为主流的适配方案。

文章介绍的比较粗略，若有错误欢迎指出。
