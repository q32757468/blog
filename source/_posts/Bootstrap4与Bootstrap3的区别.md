---
title: Bootstrap4与Bootstrap3的区别
date: 2019-06-14 17:24:35
tags:
 - CSS
 - Bootstrap
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;刚接触bootstrap是学习的b3，但是b4已经出来了有一段时间了，而且试过b4之后我更加的喜欢b4，因为二者之间还是有很多差别的，所以这里介绍一下二者的差别。
<!-- more -->

### Bootstrap4特点
1. 新增网格层适配了移动端；
2. 全面引入ES6新特性（重写所有JavaScript插件）；
3. css文件减少了至少40%；
4. 所有文档都用Markdown编辑器重写；
5. 放弃对IE8的支持

{% raw %}
<table>
<thead>
<tr>
<th align="left">Bootstrap3</th>
<th align="left">Bootstrap4</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">Less</td>
<td align="left">Sass语言编写</td>
</tr>
<tr>
<td align="left">4种栅格类</td>
<td align="left">5种栅格类</td>
</tr>
<tr>
<td align="left">使用px为单位</td>
<td align="left">使用rem和em为单位（除部分margin和padding使用px）</td>
</tr>
<tr>
<td align="left">使用push和pull向左右移动</td>
<td align="left">偏移列通过offset-类设置</td>
</tr>
<tr>
<td align="left">使用float的布局方式</td>
<td align="left">选择弹性盒模型（flexbox）</td>
</tr>
</tbody>
</table>
{% endraw %}

### 栅格系统方面的区别

#### Bootstrap3的4种栅格：

* 特小（col-xs-） 适配手机(<768px)
* 小（col-sm-） 适配平板(≥768px)
* 中（col-md-） 适配电脑(≥992px)
* 大（col-lg-） 适配宽屏电脑(≥1200px)

##### Bootstrap4的5种栅格：

* 特小（col-）(<576px)
* 小（col-sm-）(≥576px)
* 中（col-md-）(≥768px)
* 大（col-lg-） (≥992px)
* 特大（col-xl-）（≥1200px）


### 一些类方面的区别

#### img-circle与新版本中的rounded-circle
老版本中``img-circle``只对图片 而新版本中``rounded-circle``对所有元素全部生效

#### 媒体对象
去除了``media-left`` ``media-right`` 只有``media-body`` 在其中会自适应 在body前写一个图片即使left,在body之后便是right

#### display系列 / 偏移 / 外边距 / 内边距
* 用``d-xx``来设置display属性
* 偏移 不再使用 ``col-[尺寸]-offset-[x]`` 而是 ``offset-[尺寸]-[x]``
* 内外边距可以使用``m-5``这种方式来书写例如：``ml-3`` ``pt-4``
>并且以上两项都可以通过设置对应分辨率来控制，如：``ml-md-3``

#### 颜色
``badge-[]`` 对应 bs3 label
可以设置``outline``比如``btn-outline-success``

#### 新增flex类
可以通过类来设置flex，名称与css样式的属性相同

### 导航栏
这是bs3的tabs导航栏
```html
<ul class="nav nav-tabs">
  <li role="presentation" class="active"><a href="#">Home</a></li>
  <li role="presentation"><a href="#">Profile</a></li>
  <li role="presentation"><a href="#">Messages</a></li>
</ul>
```

bs4是这样写：
```html
<ul class="nav nav-tabs">
    <li class="nav-item">
        <a class="nav-link active" href="#">Item 1</a>
    </li>
    <li class="nav-item">
        <a class="nav-link disabled" href="#" tabindex="-1" aria-disabled="true">Item 2</a>
    </li>
</ul>
```
可以看出导航的内容中不再需要添加``role="presentation"``，并且bs4中使用``nav-item``来规定导航中的内容

导航栏中使用``mr-auto`` ``ml-auto`` ``m-auto`` 来使导航栏中的内容居左/右/中