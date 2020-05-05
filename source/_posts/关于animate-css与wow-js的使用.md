---
title: 关于animate.css与wow.js的使用
date: 2019-06-05 18:26:37
tags:
 - CSS
 - JS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在实际的网站开发过程中或多或少的都会使用到一些动画功能，所以学会使用动画是必不可少的，前面我们已经学习过了jQuery中的一些动画相关方面的方法，并且学习了自定义动画，可以帮我们做出我们想要的动画，当时使用这些动画还是不够简单方便，其实在网上已经有很多动画相关的扩展，我们只需要调用即可简单的实现一些常用的动画，这次就来学习一下animate.css以及wow.js的使用。
<!-- more -->
### animate.css
* animate.css是一款常见的css库，包含了很多的常见动画，使用起来也是十分的简单方便，只需要将animate.css文件引入到我们的项目中即可
``    
<link rel="stylesheet" href="./../../css/animate.css/animate.css">
`` 
* 引入进来以后我们想要使用动画也是十分的简单，只需要给我们想要添加动画的元素添加上类即可，例如：
```html
<div class="col-md-6 p-5 bg-danger animated bounce">
```
这样我们就很轻松的给这个元素加上了一个弹起动画
* 那么我们要如何给动画设置一些属性呢，比如说像延时，动画时间之类的呢
一种方法是通过css样式去覆盖原有的属性:
例如：
```html
<div class="col-md-6 p-5 bg-danger animated bounce" style="animation-delay: 5s;animation-duration: 3s"> 
```
* 以下是常用的设置：
{% raw %}
<table>
        <tr>
            <td>animation-duration</td>
            <td>规定完成动画所花费的时间，以秒或毫秒计。</td>
        </tr>
        <tr>
            <td><a href="http://www.w3school.com.cn/cssref/pr_animation-timing-function.asp">animation-timing-function</a></td>
            <td>规定动画的速度曲线。</td>
        </tr>
        <tr>
            <td>animation-delay</td>
            <td>规定在动画开始之前的延迟。</td>
        </tr>
        <tr>
            <td>animation-iteration-count</td>
            <td>规定动画应该播放的次数。</td>
        </tr>
</table>
{% endraw %}
通过这种办法我们就可以对动画进行一些设置

* 那么还有一种办法就是使用js对其进行设置
在网上有一款js插件就是基于animate.css的js动画插件，用法其实是和animate.css是差不多的，但是可以直接设置动画相关的一些属性，使用起来可能会稍微方便那么一点点，因为不用特地再去设置css。

### wow.js
上面提到的那款js插件就是wow.js，它是基于animate.css制作的，所以使用它需要在网页中引入animate.css，用法与animate.css相似：
* 使用之前需要先进行初始化：
``new WOW().init();``
* 给需要设置动画的元素添加上``wow``、``animate``类即可添加上动画，此处的animate指的是animate.css中支持的动画名称。例如：
```html
<div class="col-md-6 p-5 bg-light wow fadeInLeft">
```
通过这样的设置就可以轻松的将动画轻松的应用到元素上面
* 通过wow.js对动画进行相关的设置是比较轻松的：
data-wow-duration （动画持续时间）
data-wow-delay （动画延迟时间）
data-wow-offset（元素的位置露出后距离底部多少像素执行）
data-wow-iteration（动画执行次数）

### 总结
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;给元素添加动画的方法有很多，可以自己用css编写，或者js编写，也可以直接用网上众多的动画库，今天我这里就介绍了两款动画库。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;那么该用哪个呢，其实我个人更倾向于直接使用animate.css，因为不用引入其他更多的文件，虽然设置属性的时候不如wow.js方便，但是文件更少，并且可以只取自己想要的动画。
