---
title: JS鼠标滚轮事件
date: 2019-08-13 21:16:11
tags:
 - JS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们经常会有需求需要实现可以通过鼠标滚轮来进行翻页或者是对应用的切换，那么这个时候就需要用到鼠标滚轮事件了，并且大部分时候都需要进行一定的优化以提升用户体验，这也是我在这段时间开发chrome插件时所遇到的一些问题，来看看我是怎么解决的。
<!-- more -->
### 基本使用
鼠标滚轮事件相较于其他基本的事件使用起来会稍微麻烦那么一点点，我这里采用的jQuery的写法：
```js
      // 因为firefox浏览器对于鼠标滚轮事件的处理和其他浏览器不太一样，所以这里要注意兼容处理
      $(document).on('mousewheel DOMMouseScroll', function (e) {
        e = e || window.event;
        let delta = e.originalEvent.wheelDelta || e.originalEvent.detail; //后者是对Firefox的兼容
        if (delta > 0) {  
        // 这里写滚轮上滚时的代码
          console.log('上滚');
        }
        if (delta < 0) {
        // 这里写滚轮下滚时的代码
          console.log('下滚');
        }
      });
```
这就是最简单的使用方式了。但是如果我们直接这样使用的话会出现一个问题，那就是当我们快速的滚动多次鼠标滚轮，那么此时就会触发多次的事件，其实这个时候用户体验是很不好的，因为大部分人都会习惯滑鼠标滚轮不止一格，并且有些鼠标也不好控制轻轻的只划一格，那么这个时候就需要进行一定的优化来解决这个问题了。

### 优化方式一
方式一的优化原理就是在一次鼠标滚轮事件之后将滚轮事件与元素解绑，然后用定时器来设置一定时间后再次进行绑定：
```js
      function setDiff() {
        $(document).on('mousewheel DOMMouseScroll', function (e) {
          e = e || window.event;
          let delta = e.originalEvent.wheelDelta || e.originalEvent.detail;
          $(document).off("mousewheel DOMMouseScroll");
          if (delta > 0) { //当滑轮向上滚动时  
            console.log('上滚');
          }
          if (delta < 0) { //当滑轮向下滚动时  
            console.log('下滚');
          }
          setTimeout(setDiff, 500);
        });
      }
      setDiff();
```
当这样优化之后，无论怎么样的滚动鼠标滚轮，两次滚轮事件之间最少都要间隔 500ms 了。

虽然这样的方式是可行的，但是也存在着极端的情况，加入用户进行了一次超级长的鼠标滚轮，那么就可能触发多次滚轮事件，而我在开发chrome插件的时候预期想要的效果是在一次连续的滚动过程中，无论连续滚动了多长时间，只有第一次滚轮事件会生效，后面的都会无效，因为如果实现这样的效果就无需设置时间来控制事件的触发频率，大大的增加灵活性。后面经过思考与尝试，我还是实现了我的思路。


### 优化方式二
为了实现前面所提到的效果最终我想到可以通过判断两次事件触发的间隔来确定是否要执行事件：
```js
      function setDiff() {
        let preTime = new Date();
        $(document).on('mousewheel DOMMouseScroll', function (e) {
          let diff = (new Date()) - preTime;
          if (diff > 300) {
            e = e || window.event;
            let delta = e.originalEvent.wheelDelta || e.originalEvent.detail;  //后者Firefox
            // $(document).off("mousewheel DOMMouseScroll");
            if (delta > 0) { //当滑轮向上滚动时  
              console.log('上滚');
            }
            if (delta < 0) { //当滑轮向下滚动时  
              console.log('下滚');
            }
          }
          preTime = new Date();
        });
      }
      setDiff();
```
这样优化之后，只要是连续的滚动鼠标滚轮，那么一定只会触发第一次事件，当用户松开滚轮，进行下一次滚动时，间隔时间够长，就又可以触发一次滚轮事件，但是后面连续的所有事件因为间隔时间十分之短，就不会被触发，就达到了我的目的。

### 总结
这也是我第一次使用鼠标的滚轮事件，结合网上的一些内容以及自己的一些思考，最终还是完成了自己的需求，并且对代码进行了简化，虽然花了一些时间，但是肯定是值得的。

