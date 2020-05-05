---
title: jQuery学习（入口函数与冲突问题）
date: 2019-05-10 12:39:20
tags:
 - JS
 - jQuery
categories:
 - 学习笔记
 - jQuery学习笔记
---
### 前言
> jQuery入口函数与原声js入口函数的区别，以及解决冲突问题，还有一些小细节，帮助我学习。
<!-- more -->
### jQuery入口函数
* 多个window.onload只会执行一次, 后面的会覆盖前面的
* 多个$(document).ready()会执行多次,后面的不会覆盖前面的
* 不会覆盖的本质(了解,后面jQuery原理会详细讲解) 
jQuery框架本质是一个闭包,每次执行我们都会给ready函数传递一个新的函数,不同函数内部的数据不会相互干扰

{% raw %} 
<table>
<thead>
<tr>
<th></th>
<th>window.onload</th>
<th>$(document).ready()</th>
</tr>
</thead>
<tbody>
<tr>
<td>执行时机</td>
<td>必须等待网页全部加载完毕(包括 图片等),然后再执行包裹代码</td>
<td>只需要等待网页中的DOM结构 加载完毕,就能执行包裹的代码</td>
</tr>
<tr>
<td>执行次数</td>
<td>只能执行一次,如果第二次,那么 第一次的执行会被覆盖</td>
<td>可以执行多次,第N次都不会被上 一次覆盖</td>
</tr>
<tr>
<td>简写方案</td>
<td>无</td>
<td>$(function () { });</td>
</tr>
</tbody>
</table>
{% endraw %}

### 解决$符号冲突问题
* 为什么是window.jQuery = window.$ = jQuery;,而不是window.jQuery  = jQuery;

jQuery框架之所以提供了jQuery访问还提供$访问,就是为了提升开发者的编码效率

* $符号冲突怎么办?

很多js的框架都提供了类似jQuery这样的便捷访问方式,所以很有可能某一天我们在使用多个框架的时,多个框架作者提供的便捷访问方式冲突(A框架通过$访问,B框架也通过$访问)



***释放$使用权

当便捷访问符号发生冲突时,我们可以释放$使用权, 释放之后只能使用jQuery



