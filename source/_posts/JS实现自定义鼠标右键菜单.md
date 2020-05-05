---
title: JS实现自定义鼠标右键菜单
date: 2019-09-12 15:27:35
tags:
 - JS
 - chrome插件开发
categories:
 - 页面小功能
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最近在我开发chrome扩展插件的过程中准备实现通过鼠标右键来打开一个菜单来添加删除导航以及修改背景图片等一系列的功能，这里来记录一下具体是如何实现这样的功能的以及注意实现。
<!-- more -->

这里通过一个小demo来进行演示，整体还是挺简单的，只要注意一些细节就好了。

### 先来看一下效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/KnKE48CSRXQlxQPSEETunFPfZa1dZW0UItDZYcYcmBE!/b/dAYBAAAAAAAA&bo=kgJQAgAAAAADB.A!&rf=viewer_4"> 
这个小的菜单框就是我自定义的鼠标右键菜单，这里没有太多的修饰，样式根据情况进行适当的调整就可以了。

### HTML部分代码
```html
<!-- 这个box模拟页面中的一部分 -->
  <div class="box"></div>

<!-- 而这个列表就是我们需要的右键菜单了 -->
  <ul class="list">
    <li>菜单一</li>
    <li>菜单二</li>
    <li>菜单三</li>
    <li>菜单四</li>
  </ul>
```

### CSS部分
```css
<style>
  * {
    padding: 0;
    margin: 0;
  }

  .box {
    width: 400px;
    height: 400px;
    border: 1px solid #000;
    margin: 0 auto;
    margin-top: 20px;
  }

  ul {
    position: fixed;
    /* 这个菜单默认是不显示的 */
    display: none;
    list-style: none;
    border: 1px solid #000;
  }
</style>
```
css唯一要注意的就是默认这个菜单是不显示的。

### JS部分
```js
// 这里用了jQuery来写，不用也是差不多的
 <script src="./js/jquery-3.4.1.min.js"></script>
  <script>
    $(function () {
      const list = $('.list');
      const box = $('.box');
      // 鼠标右键事件
      box.contextmenu(function (e) {

        // 在return false 之前可以写自己想要的功能的代码
        e = e || window.event;
        // 将菜单的位置设置为鼠标右键时的位置
        list.css({
          'top': e.pageY + 'px',
          'left': e.pageX + 'px',
        });
        list.show();//将菜单显示出来

        //禁用了原本的鼠标右键菜单
        return false;
      })

      // 单击网页任意位置使菜单隐藏
      $(window).click(function () {
        list.hide();
      })
    })
  </script>
```
在注释中已经把需要注意的地方写出来了，这里在总结一下：

+ contextmenu 事件是鼠标右键的事件

+ 基本的原理就是通过鼠标右键的事件将我们自己定义的右键菜单定位到鼠标指针所在的位置，并且将其显示出来，然后鼠标左键单击时需要将这个菜单进行隐藏。

+ 一定要将逻辑代码写在鼠标右键事件中的 ``return false``之前。

### 总结
鼠标右键自定义菜单本身挺简单的，这里在开发chrome插件的过程中使用到了，所以记录一下。

