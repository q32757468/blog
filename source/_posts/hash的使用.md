---
title: HTML中DOM的hash属性的使用
date: 2019-05-26 15:53:50
tags:
 - JS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;hash在网站中的使用也是十分的常见，使用hash也可以带来便利，这次我们来看看hash究竟该如何让使用。
<!-- more -->
### 如何设置hash
我们可以通过在js中设置hash的值
```js
<script>
window.location.hash=3;
</script>
```
这样就可以设置hash值，那么设置过后我们就可以在地址栏看到多了一个#3
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/H8tyFYP6W*cxDr1aDqapb7YlavnaAimWRkmYblIM5io!/b/dFMBAAAAAAAA&bo=xwVmAAAAAAADB4Y!&rf=viewer_4">

### 如何获取hash的值
```js
<script>
    window.location.hash=3;
    console.log(window.location.hash); //#3    
</script>
``` 

直接就能获取hash的值，但是这样获取到的值带有``#``
我们要获取的一般都是不带``#``的值
所以我们可以这样处理

```javasript
<script>
    window.location.hash=3;
    console.log(window.location.hash); //#3   
    console.log(window.location.hash.substring(1)); //3    
</script>
```
使用字符串截取函数，截取不带``#``的这一部分

### 监听地址栏hash值的变化
经常会有需求需要监听地址栏中hash值的变化，实现路由的功能，那么是如何实现的呢：
```js
// 当监听到变化时执行的函数
 function hashChange() {
        let urlHash = window.location.hash;
        const hashDom = '#test'
        if (urlHash == hashDom) {
          $(hashDom).show();
        }
      }
      hashChange();

      if (('onhashchange' in window) && ((typeof document.documentMode === 'undefined') || document.documentMode == 8)) {
        // 浏览器支持onhashchange事件
        window.onhashchange = hashChange;  // TODO，对应新的hash执行的操作函数
      } else {
        // 不支持则用定时器检测的办法
        setInterval(function () {
          // 检测hash值或其中某一段是否更改的函数， 在低版本的iE浏览器中通过window.location.hash取出的指和其它的浏览器不同，要注意
          let ischanged = isHashChanged();
          if (ischanged) {
            hashChange();  // TODO，对应新的hash执行的操作函数
          }
        }, 150);
      }
```
这里的这个例子实现的就是通过通过超链接来进行hash的跳转，并且将原本隐藏的路由元素显示出来。



### 总结
hash的使用十分的简单，并且使用频率很高，通过hash我们就可以实现例如翻页还在同一页的效果。这里也通过一个简单的例子实现了hash的应用。
