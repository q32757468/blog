---
title: 使用vue来实现选项卡
date: 2019-08-06 10:54:14
tags:
 - VUE
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前面有学习过如何使用原生js或者是jQuery来实现选项卡的功能，原理其实都差不多是一样的，那么现在学习过vue，试着来使用vue来实现选项卡的功能，理解vue开发的思路。
<!-- more -->
### 原理
其实选项卡功能的原理大体上都是一样的，都是通过鼠标经过来添加样式并且删除其他兄弟的样式来实现的，但是我们在使用vue来开发的过程中会有很大不一样的地方就是使用vue并不需要操作元素，只需要关心业务逻辑就好了，所以其实就可以更加简单的实现选项卡的功能。


### 实现
#### HTML部分 
```html
  <div id="app">
    <tabs></tabs>
  </div>

//定义模板
  <template id="tabs">
    <div class="wrap">
      <ul class="tab_h">
        <li v-on:mouseover="toActive(index)" :class="{cur:item.active}" v-for="(item,index) in tabList" :key="index">
          {{item.head}}
        </li>
      </ul>
      <ul class="tab_b">
        <li v-show="item.active" v-for="(item,index) in tabList" :key="index">{{item.body}}</li>
      </ul>
    </div>
  </template>
```
这里是在html中定义的组件模板，其实这个在哪定义模板不重要，重要的是这上面的思路。

#### JS部分
```js
  <script src="./../../js/vue.js"></script>
  <script>
    const vm = new Vue({
      el: '#app',
      components: {
        tabs: {
          template: '#tabs',
          data() {
            return {
              //将所有的数据都存放在这个数组中，遍历数组就可以得到所有的数据
              tabList: [
                { 'head': '选项一', 'body': '这是选项一中的内容', 'active': true },
                { 'head': '选项二', 'body': '这是选项二中的内容', 'active': false },
                { 'head': '选项三', 'body': '这是选项三中的内容', 'active': false },
                { 'head': '选项四', 'body': '这是选项四中的内容', 'active': false },
              ]
            }
          },
          methods: {
            //定义鼠标经过时的方法
            toActive(index) {
              this.tabList.forEach(item => item.active = false);
              this.tabList[index].active = true;
            }
          },
        }
      }
    })
  </script>
```
可以看出使用vue来实现这样的功能是十分的方便的，不用像原生那样操作dom元素。

#### CSS部分
```css
<style>
  * {
    margin: 0;
    padding: 0;
    list-style: none;

  }

  .wrap {
    margin-left: 400px;
    margin-top: 20px;
  }

  .tab_h,
  .tab_b {
    display: flex;
    width: 800px;
  }

  .tab_h li {
    flex-grow: 1;
    text-align: center;
    border: 1px solid black;
  }

  .tab_b li {
    border: 1px solid black;
    border-collapse: collapse;
    width: 100%;
    height: 200px;
  }

  .cur {
    display: block;
    color: orange;
    font-weight: bold;
  }
</style>
```
css部分就不重要了，主要是激活时的样式。

#### 效果
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/lg.L3FE0o1xfcEINrSLKpQY7Ddin2CzPnBOIpWOgwh4!/b/dL4AAAAAAAAA&bo=BQVzAQAAAAADB1A!&rf=viewer_4">


### 总结
在学习vue的过程中我时常会想如何通过vue来实现一些我们平时比较常见的功能，通过原生来实现的思路已经非常清除了，那么如何将将同样的思路转化在vue中实现，那么通过实现这次的选项卡的小例子算是见到了vue的强大简便之处，一样的思路，换了一种实现的方式，但是却会更加的简便，vue确实是一款非常好用的框架，特别是这种思路太让人佩服了。
