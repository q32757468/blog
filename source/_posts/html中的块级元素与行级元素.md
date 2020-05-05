---
title: html中的块级元素与行级元素
date: 2019-05-10 09:15:48
tags:
 - CSS
categories:
---
### 前言
> 在最开始的时候没有清除的认识到块级元素与行级元素的差别，也没有想到二者之间有如此大的差别，以及二者之间的关系以下是我对二者的学习总结：
<!-- more -->
### 块级元素和行内元素的定义
#### 块级元素：
* 总是在新行上开始；
* 高度，行高以及外边距和内边距都可控制；
* 宽度缺省是它的容器的100%，除非设定一个宽度。
* 它可以容纳内联元素和其他块元素

#### 行内元素？
* 和其他元素都在一行上；
* 高，行高及外边距和内边距不可改变；
* 宽度就是它的文字或图片的宽度，不可改变
* 内联元素只能容纳文本或者其他内联元素

**对行内元素，需要注意如下：**
* 设置宽度width 无效。
* 设置高度height 无效，可以通过line-height来设置。
* 设置margin 只有左右margin有效，上下无效。
* 设置padding 只有左右padding有效，上下则无效。注意元素范围是增大了，但是对元素周围的内容是没影响的。 
### 可以通过display对二者进行调整
display属性表：

{% raw %}
<table>
<tr>
<th>值</th>
<th>描述</th>
</tr>
<tr>
<td>none</td>
<td>此元素不会被显示。</td>
</tr>
<tr>
<td><strong>block</strong></td>
<td>此元素将显示为块级元素，此元素前后会带有换行符。</td>
</tr>
<tr>
<td><strong>inline</strong></td>
<td>默认。此元素会被显示为内联元素，元素前后没有换行符。</td>
</tr>
<tr>
<td><strong>inline-block</strong></td>
<td>行内块元素。（CSS2.1 新增的值）</td>
</tr>
<tr>
<td>list-item</td>
<td>此元素会作为列表显示。</td>
</tr>
<tr>
<td>run-in</td>
<td>此元素会根据上下文作为块级元素或内联元素显示。</td>
</tr>
<tr>
<td>compact</td>
<td>CSS 中有值 compact，不过由于缺乏广泛支持，已经从 CSS2.1 中删除。</td>
</tr>
<tr>
<td>marker</td>
<td>CSS 中有值 marker，不过由于缺乏广泛支持，已经从 CSS2.1 中删除。</td>
</tr>
<tr>
<td>table</td>
<td>此元素会作为块级表格来显示（类似 &lt;table&gt;），表格前后带有换行符。</td>
</tr>
<tr>
<td>inline-table</td>
<td>此元素会作为内联表格来显示（类似 &lt;table&gt;），表格前后没有换行符。</td>
</tr>
<tr>
<td>table-row-group</td>
<td>此元素会作为一个或多个行的分组来显示（类似 &lt;tbody&gt;）。</td>
</tr>
<tr>
<td>table-header-group</td>
<td>此元素会作为一个或多个行的分组来显示（类似 &lt;thead&gt;）。</td>
</tr>
<tr>
<td>table-footer-group</td>
<td>此元素会作为一个或多个行的分组来显示（类似 &lt;tfoot&gt;）。</td>
</tr>
<tr>
<td>table-row</td>
<td>此元素会作为一个表格行显示（类似 &lt;tr&gt;）。</td>
</tr>
<tr>
<td>table-column-group</td>
<td>此元素会作为一个或多个列的分组来显示（类似 &lt;colgroup&gt;）。</td>
</tr>
<tr>
<td>table-column</td>
<td>此元素会作为一个单元格列显示（类似 &lt;col&gt;）</td>
</tr>
<tr>
<td>table-cell</td>
<td>此元素会作为一个表格单元格显示（类似 &lt;td&gt; 和 &lt;th&gt;）</td>
</tr>
<tr>
<td>table-caption</td>
<td>此元素会作为一个表格标题显示（类似 &lt;caption&gt;）</td>
</tr>
<tr>
<td>inherit</td>
<td>规定应该从父元素继承 display 属性的值。</td>
</tr>
</table>
{% endraw %}

#### 这是一个小测试：
```html
<style>
  div{
      border: 1px solid blue;
      margin-bottom: 10px;
  }
  span{
      border: 1px solid red;
      height: 50px;
      margin: 40px;
      /* 可以发现对span这个行内元素设置了height属性但是其并没有生效 
      设置了margin，但只有左右margin生效，padding同理。
      */
  }
  .div3{
    display: inline;
    /* 将其设为行内元素，其只拥有行内元素的特性 */
    
  }
  .div4{
      display: inline-block;
      /* 将其设置为行内块级元素，兼具二者特性 */
      height: 50px;
  }
</style>
<body>
<div>这是第一个div:div是块级元素，默认宽度是100%,可以设置宽度与高度，默认自动占一行</div>
<div>这是第二个div<span>这是一个块级元素:大小只与内容有关，不可以设置宽度与高度，但能设置行高，默认不占一行</span></div>
<div">这是第三个div：通过display:inline将其变为行内元素</div>
<div">这是第四个div:通过display:inline-block将其变为行内块级元素，兼具二者特性</div>
</body>
```
效果图：
<img width="200" src="http://a4.qpic.cn/psb?/V131x4904WMIoW/xJJqXTbdnn04mx45pC3lgNpi9dhpEKTdkM3Q3ubdCrw!/c/dL8AAAAAAAAA&ek=1&kp=1&pt=0&bo=7wTJAgAAAAADFxI!&tl=1&vuin=32757468&tm=1557453600&sce=60-2-2&rf=0-0" />
***
以上就是我的学习总结。

