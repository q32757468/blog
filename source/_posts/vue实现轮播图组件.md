---
title: vue实现轮播图组件
date: 2019-08-08 19:53:49
tags:
 - 动画
 - JS
 - VUE
categories:
 - 页面小功能
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前面已经介绍实现过了常见的一些轮播图，也对其实现原理十分的了解了，那么在vue中要如何实现轮播图呢，其实也不难，因为原理都是一样的，只是实现的形式有所区别。这里就以实现淡入淡出轮播图为例。
<!-- more -->
### 原理
实现淡入淡出轮播图的原理其实也都是一样的，前面也提到了，就是将要轮播的所有图片都叠在一起，默认只显示第一张，然后通过定时器来控制图片的切换，并且加上淡入淡出的动画效果，在vue中实现起来还要更加的方便。

### 实现
template部分：
```html
  <template id="banner">
    <div class="bannerWrap" @mouseover="suspend" @mouseout="autoPlay" @blur="suspend" @focus="autoPlay">
      <button class="pre" @click="pre">PRE</button>
      <button class="next" @click="next">NEXT</button>
      <transition-group tag="ul" class="imgList" name="fade">
        <li v-show="item.cur" v-for="(item, index) in list" :key="item.src"><img :src=item.src alt=""></li>
      </transition-group>
      <ul class="optionList">
        <li v-for="(item, index) in list" :key="item.src" @click="jump(index)"></li>
      </ul>
    </div>
  </template>
```

js部分：
```js
    const vm = new Vue({
      el: '#app',
      components: {
        banner: {
          template: '#banner',
          data() {
            return {
              timer: null,
              index: 0,
              // 图片的地址以及是否显示
              list: [
                { 'src': './images/beach-exotic-holiday-248797.jpg', 'cur': true },
                { 'src': './images/blur-calm-waters-dawn-395198.jpg', 'cur': false },
                { 'src': './images/dark-flash-lightning-1114690.jpg', 'cur': false },
                { 'src': './images/mountain-range-mountains-114979.jpg', 'cur': false },
              ]
            }
          },
          methods: {
            // 定义自动播放函数，通过调用next方法来实现
            autoPlay() {
              clearInterval(this.timer)//还是一样，开启定时器之前需要先清除一下，防止bug
              this.timer = setInterval(this.next, 2000)
            },
            // 定义切换到下一张图片的方法
            next() {
              this.index++;
              if (this.index >= this.list.length) {
                this.index = 0;
              }
              this.list.forEach(item => {
                item.cur = false;
              });
              this.list[this.index].cur = true;
            },
            // 定义切换到前一张图片的方法
            pre() {
              this.index--;
              if (this.index <= -1) {
                this.index = this.list.length - 1;
              }
              this.list.forEach(item => {
                item.cur = false;
              });
              this.list[this.index].cur = true;
              console.log(this.index);
            },
            // 暂停的方法
            suspend() {
              clearInterval(this.timer)
            },
            // 点击选项圆点进行跳转的方法
            jump(index) {
              this.index = index;
              this.list.forEach(item => {
                item.cur = false;
              });
              this.list[index].cur = true;
            }
          },
          mounted() {
            // 自动播放函数以及失焦暂定获焦开启都需要在mounted阶段设置
            this.autoPlay();
            // 直接在mounted中写聚焦以及失焦的函数
            window.blur = function () {
              clearInterval(this.timer)
            }
            window.focus = function () {
              autoPlay()
            }
          }
        }
      }
    })
```
整体的思路和前面使用jQuery写的轮播图的思路是一致的，只不过是换了一种方式。


样式部分：
```css
  * {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  .bannerWrap {
    width: 800px;
    margin-left: 400px;
    margin-top: 20px;
    position: relative;
  }

  .imgList {
    display: flex;
    position: relative;
    width: 800px;
    height: 400px;
  }

  .imgList li {
    position: absolute;
    left: 0;
  }

  .imgList img {
    width: 800px;
    height: 400px;
  }

  button {
    position: absolute;
    width: 60px;
    height: 40px;
    top: 50%;
    transform: translateY(-50%);
    z-index: 2;
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

/* 实现动画的两组类 */
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity .5s;
  }

  .fade-enter,
  .fade-leave-to {
    opacity: 0;
  }
```
样式部分要注意的就是实现动画效果的两组类，不能设置错了。

通过以上代码就可以实现一个淡入淡出的轮播图了，非常的简便，代码也相较不使用vue开发要少的多。

### 总结
这次通过vue实现了一个淡入淡出的轮播图组件，原理都是一样的，只不过是使用vue来开发会更加简便。滑动轮播图也是类似的，这里就不演示了。重要的是熟悉vue的开发思路以及开发原理。