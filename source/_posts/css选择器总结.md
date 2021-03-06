---
title: css选择器总结
date: 2019-05-08 19:11:10
tags:
 - CSS
categories:
---
### 前言
> 在编写css代码时合理使用选择器有助于提升编写效率以及缩短代码量，所以对常用的选择器熟练使用是十分有必要的，以下为一些选择器的使用总结。
<!-- more -->
### 基础的一些选择器
标签选择器，以标签名作为选择器名
类选择器，通过"."+类名作为选择器名称如：
``.test``
id选择器，通过"#"+类名作为选择器名称如：
``#test``

### 交集选择器
标记选择器+类选择器或id选择器，中间不能有任何的空格如：
``p.aaa或p#aaa``
注意：后代选择器作用于所有后代。（与子代选择器的区分）


### 后代选择器：
由两个标记选择器组成，第一个为父级，第二个为子代，中间一定要有空格
``div p``
注意：子代选择器作用于所有后代。（与后代选择器的区分）

### 子代选择器
此时选择到的是作为子代的p 子代选择器与后代选择器的区别，子代选择器只作用于后一代儿子，而不会影响到孙子，后代选择器作用于所有后代
``
div>p
``

### 并集选择器：
由若干个任意选择器组成，由逗号分隔开可以同时对多个目标生效。
``h1,.aaa,#bbb``

### 属性选择器
假设有html代码：
```html
<input name="test" placeholder="初始值" class="test" id="test" type="text" value=""> 
```
``E[att^=value]`` E可以省略，满足  **属性值**      等于value
``input[name^=test]``
``E[att$=value]`` E可以省略，满足 **属性值后缀**   等于value
``input[name$=st]``
``E[att*=value]`` E可以省略，满足 **属性值包含**   value
``input[name*=es]``

### 兄弟选择器
p+h2 选择到的是p后面的   **相邻的**   h2
p~h2 选择到的是p后面的   **不一定相邻的**   h2

### 伪类选择器：
:root 该选择器对页面所有元素的生效，可以用其他选择器将其覆盖
:not   该选择器可以将选定内容排除在大选择器之外（body :not(p)）此时选择器就不会选择到p标记。冒号之前必须有空格


### 伪元素选择器：
:first-letter 	向文本的第一个字母添加样式
:first-line		向文本的首行添加特殊样式。
:before		在元素之前添加内容。
:after		在元素之后添加内容。

### 链接伪类：
a:link         未访问时的状态
a:visited    访问后的状态
a:active      鼠标单击不动时的状态
a:hover      悬停时的状态	

### 关于选择器的优先级
比较同一级别的个数，数量多的优先级高，如果相同即比较下一级别的个数
important > 内联 > ID > 类 > 标签 | 伪类 | 属性选择 > 伪对象 >  通配符 > 继承