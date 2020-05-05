---
title: 详解使用jQuery实现滑动轮播图
date: 2019-08-07 21:48:50
tags:
 - JS
 - jQuery
 - 动画
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;网页中的轮播图十分的常见，并且轮播图的动画样式也有很多种，前面已经有介绍过淡入淡出的轮播图了，比较的简单，这次来详细介绍一下滑动轮播图，并且优化了之前的很多细节，一起来看一看。
<!-- more -->
### 原理
在实现之前先介绍一下滑动轮播图实现的原理，这里分为两种来介绍，常见的滑动轮播图有两种，一种是无限循环滑动的，一种是会往滑回开始位置的，先来讲讲这二者的原理以及区别：

#### 会滑回的
通过图片来更好的理解：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/capaDv9kmW.wvAf0Nf.iNakYE9IUQ28dZJhLLcFol5k!/b/dL4AAAAAAAAA&bo=7gahAgAAAAADB2k!&rf=viewer_4">
通过这张图片我们就很好理解了，每次轮播其实就是将三张图片当成一个整体向左移一张图片的宽度实现，当到达最左边或者最右边的时候再通过动画滚回第一张或者是最后一张图片，这是一种比较常见的方式。

#### 无限滑动的
也一样通过图片来更好的理解：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/3HPElTrJOiNc5U*AqwzOl13jpj.nXXQ9Ya8Im0Cyc.s!/b/dLYAAAAAAAAA&bo=cQXNAAAAAAADB5s!&rf=viewer_4">
其实无限滑动的基本原理都是和上面的会滑回的是一样的，最大的区别就在于当到达最左边或者最右边时，这次就不是通过动画来过渡了，而是通过在图片列表的开头添加一张最后的图片以及列表末尾添加一张开头的图片，当轮播到最后一张图片的时候通过修改css样式跳回到第一张图片，由于在末尾添加了一张开头的图片，所以看不出来这个跳转的过程，这样就实现了无限滑动的效果。

### 实现
#### 普通版
HTML部分：
```html
  <div class="bannerWrap">
    <button class="pre">PRE</button>
    <button class="next">NEXT</button>
    <ul class="imgList">
      <li><img src="./images/cool-wallpaper-dawn-hd-wallpaper-66997.jpg" alt=""></li>
      <li><img src="./images/flowers-4352530_1280.jpg" alt=""></li>
      <li><img src="./images/hd-wallpaper-mountain-range-mountains-114979.jpg" alt=""></li>
    </ul>
    <ul class="optionList">
      <li></li>
      <li></li>
      <li></li>
    </ul>
  </div>
```
html部分没有什么好讲的，按照结构布局好就可以了。

js部分：
```js
  <script src="./js/jquery-3.4.1.min.js"></script>
  <script>
    $(function () {
      const bannerWrap = $('.bannerWrap');
      const imgList = $('.imgList');
      const optionList = $('.optionList');
      const preBt = $('.pre');
      const nextBt = $('.next');
      let timer;
      const imgWidth = bannerWrap.width();
      const len = imgList.children().length;
      let index = 0;

      // 自动轮播的函数，通过调用向右滑动的函数来实现轮播
      function autoSlide() {
        timer = setInterval(next, 3000)
      }
      autoSlide();

      //当鼠标经过轮播图时暂停的函数
      function suspend() {
        bannerWrap.hover(function () {
          clearInterval(timer)
        }, function () {
          // 在每次重新开启定时器之前都需要将定时器清除一遍，防止由于特殊情况定时器并未真正关闭而导致重复开启定时器
          clearInterval(timer)
          autoSlide();
        })
      }
      suspend();

      // 滑动到下一张的函数
      function next() {
        index = index >= len - 1 ? 0 : index + 1;
        // 在新的动画开始之前都需要停止一下之前的动画
        imgList.stop().animate({
          'marginLeft': -imgWidth * index
        })
      }

      // 滑动到前一张的函数
      function pre() {
        index = index <= 0 ? (len - 1) : (index - 1);//这里的 index-1 一定要打上括号，否则可能会引发bug
        imgList.stop().animate({
          'marginLeft': -imgWidth * index
        })
      }

      nextBt.click(function () {
        next();
      })
      preBt.click(function () {
        pre();
      })

      // 小圆点跳转的函数
      function jump() {
        for (let i = 0; i < len; i++) {
          optionList.children().eq(i).click(function () {
            imgList.stop().animate({
              'marginLeft': -imgWidth * i
            })
          })
        }
      }
      jump();

      //失去焦点以及获得焦点时
      $(window).blur(function () {
        clearInterval(timer)
      })
      $(window).focus(function () {
        clearInterval(timer)
        autoSlide();
      })
    })
  </script>
```
js部分最重要的就是思路，都在注释中有写，应该比较好理解

css部分：
```css
  * {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  .bannerWrap {
    width: 800px;
    margin-top: 40px;
    margin-left: 400px;
    overflow: hidden;
    position: relative;
  }

  .imgList {
    display: flex;
  }

  img {
    width: 800px;
    height: 400px;
  }

  button {
    position: absolute;
    width: 60px;
    height: 40px;
    top: 50%;
    transform: translateY(-50%);
  }

  .pre {
    left: 50px;
  }

  .next {
    right: 50px;
  }

  .optionList {
    position: absolute;
    right: 60px;
    bottom: 40px;
    display: flex;
  }

  .optionList li {
    margin-left: 5px;
    border-radius: 50%;
    width: 20px;
    height: 20px;
    border: 1px solid #fff;
  }
```
也都是一些基本的样式设置，没有什么好讲的。这里相比于以前写的轮播图来说有优化的地方就是这里并没有直接写一个自动轮播的函数，而是通过调用下一站图片的函数来实现自动轮播，这样就减少了重复代码。

#### 无限版
html部分的代码和普通版的是一样的就不放出来了

css部分代码就只有一下这一部分和普通版有所区别：
```css
   .imgList {
    display: flex;
    margin-left: -800px;
  }
```
区别就是调整 margin-left 以显示真正的第一张图片。

重点就是js部分：
```js
  <script src="./js/jquery-3.4.1.min.js"></script>
  <script>
    $(function () {
      const bannerWrap = $('.bannerWrap');
      const imgList = $('.imgList');
      const optionList = $('.optionList');
      const preBt = $('.pre');
      const nextBt = $('.next');
      let timer;
      const imgWidth = bannerWrap.width();
      let index = 1;
      //通过js来添加两张图片
      imgList.prepend(imgList.children().last().get(0).outerHTML)//一定要写在获取长度之前
      imgList.append(imgList.children().get(1).outerHTML)//一定要写在获取长度之前
      const len = imgList.children().length;

      function autoSlide() {
        timer = setInterval(next, 1000)
      }
      autoSlide();

      function suspend() {
        bannerWrap.hover(function () {
          clearInterval(timer)
        }, function () {
          clearInterval(timer)
          autoSlide();
        })
      }
      suspend();

      // 滑动到下一张的函数，由于多了两张图片，所以要注意index的值
      function next() {
        index++;
        // 这里的stop方法要格外注意一下，必须像这样加参数
        imgList.stop(false, true).animate({
          'marginLeft': -imgWidth * index
        }, function () {
          if (index >= (len - 1)) {
            index = 1;
            // 重点就是这个动画执行完毕后的回调，通过这个回调来讲图片重新调回想要的位置以实现无限
            imgList.css({
              'marginLeft': -imgWidth
            })
          }
        })
        console.log(index);
      }

      // 滑动到前一张的函数，与next函数类似
      function pre() {
        index--;
        if (index <= (-1)) {
          index = (len - 2);
          imgList.css({
            'marginLeft': -imgWidth * (index)
          })
        }

        imgList.stop(false, true).animate({
          'marginLeft': -imgWidth * index
        }, function () {
          if (index <= 0) {
            index = (len - 2);
            imgList.css({
              'marginLeft': -imgWidth * (index)
            })
          }
        })
      }

      nextBt.click(function () {
        next();
      })
      preBt.click(function () {
        pre();
      })

      // 小圆点跳转的函数，这里由于多了两张图片，所以也要稍微修改一下
      function jump() {
        for (let i = 0; i < len - 2; i++) {
          optionList.children().eq(i).click(function () {
            imgList.stop().animate({
              'marginLeft': -imgWidth * (i + 1)
            })
          })
        }
      }
      jump();

      $(window).blur(function () {
        clearInterval(timer)
      })
      $(window).focus(function () {
        clearInterval(timer)
        autoSlide();
      })
    })
  </script>
```
与普通版不一样的地方都注释出来了，与普通版思路都是差不多的，区别就在于处理最后一张图片回到第一张（或者相反）的方式上有所区别。

这里再详细讲一下``stop()``方法为什么要加参数：

+ 如果不加参数的话默认是停止当前动画继续后面的动画，当快速点击下一张图片到最后一张图片通过修改 css 跳回第一张时由于停止了动画就不会执行回调，所以就会持续出现空白。

+ 而通过给stop()方法传递参数就可以解决这一问题，``stop(false, true)``则表示立即完成当前动画，继续后面动画，这样传递参数以后就不会因为停止了动画而导致不执行回调函数了，进而就解决了问题。

### 一些要十分注意的问题
轮播图思路比较简单，但是在实现过程中要是忽略一些细节就有可能会导致意想不到的结果，这里提出来要十分注意：

+ 在开启新的动画之前一定要调用**stop()**方法讲之前的动画停止，防止作出不必要的动画。

+ 在开启重新调用自动轮播函数之前一定要先清除一遍定时器，否则可能会导致重复开启定时器。

+ 由于setInterval在后台运行时会自动停止，当重新运行时会一次性将在后台时的全部运行完，所以当重新切回轮播图时会疯狂滑动，为了避免这种情况就需要手动设置在页面失去焦点时暂停定时器，获取焦点时重新开启。

### 总结
前面有写过淡入淡出的轮播图，原理更加的简单，实现起来也更加的简单，这次通过写滑动轮播图来练练手，并且优化了代码，写的过程中也遇到了很多的问题，也都解决了，进而学习到了很多的细节。下次尝试使用vue来写一个轮播图组件。