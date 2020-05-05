---
title: uniapp实现左右滑动长列表
date: 2019-11-29 11:35:27
tags:
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最近在学习uniapp，因为我觉得这是个不错的开发工具，哪怕不用来进行多端开发，只是用来进行微信小程序的开发这也是一个非常不错的开发框架，因为主要的书写规范都是和vue类似的。这次突然想开发一个手机APP，准备仿写一个我常用的一个App，觉得它的左右滑动的长列表做得挺不错的，所以花了点时间仿写了一下。
<!-- more -->
### 先来看一下效果
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/Eiciz8i1IlxGw9pqaUyMhHFFcYi8r5ks7QOg7YBoD9M!/b/dLgAAAAAAAAA&bo=DQJYAwAAAAACN0c!&rf=viewer_4">
这里只是简单的实现了功能，样式不是很好看，不过也可以通过动图看出实现了一个长列表的功能。

### 主要功能及实现思路
#### 首先是实现一个长列表的功能
+ 这里可以分为两部分，一部分是头部，也就是用来点击跳转以及显示当前所在页面组件的头部，另一部分就是主要内容部分，就是我们的页面组件。

+ 这里实现起来非常的简单，我们需要实现想要的效果只需要头部使用``scroll-view``就可以了，就默认可以滚动了，而主体部分使用``swiper``组件就可以实现我们想要的长列表的切换功能了。

#### 将头部和主体部分结合起来
我们还需要将单独的头部和主体部分结合起来，不然这二者也只是两个毫无关联的组件而已，结合起来需要实现这些功能：
+ 左右滑动主体部分时，头部的激活状态也会跟着改变。
  这个可以通过``swiper``组件的**change**事件来动态的改变头部链接的样式。

+ 点击头部时会跳转到相应的主体部分。并且正确的改变激活状态。
  这个可以通过给每个链接绑定一个点击事件，通过这个点击事件来动态的改变``swiper``组件的``current``属性以达到改变显示哪个页面组件。
  如果要实现点击没有动画的跳转的话可以在点击的时候将``swiper``组件的``duration``动画时长属性设置为0，然后在通过 ``animationfinish``事件在动画结束后将其设置会原来的状态，不过这里要注意的一点是在 uniapp的nvue页面中``duration``属性无效，所以需要从其他地方入手修改。

+ 自适应的将头部激活的栏目居中，这个可能不是很好理解，大概意思就是当跳转到特别后面的页面组件当中的话，头部也会跟着变化，但是可能会看不到，所以需要将其显示到可视区域的中间。
  这里可以通过动态的修改``scroll-view``组件的``scroll-left``属性来实现，而``scroll-left``的值则和当前页面组件的index有关。

#### 加载组件
考虑到长列表的性能问题，如何加载组件是非常重要的：
+ 需要动态的加载组件，异步加载组件的方式有非常的多种，这里也可以参<a href="https://cn.vuejs.org/v2/guide/components-dynamic-async.html">考官方文档</a>。

+ 当页面组件还未加载时以及正在加载中时需要显示正在加载中以确保用户看到一个整洁美观的页面。
  默认所有页面都是显示 正在加载 当切换到相应的页面组件则开始加载组件，但是此时依然显示的是 正在加载 直至组件加载完毕才不显示正在加载而是显示相应的组件（我这里是通过setTimeOut来模拟的组件获取数据）。那该如何做到，可以通过子组件 $emit 父组件的方法来实现这样一个功能。

+ 当渲染的页面组件过多时结束一些组件的加载。
  可以通过一个数组来存储最近打卡的页面组件，(数量就是要缓存的组件的数量)，当每次进入到一个新打开的页面组件后，也就是打开下一个组件之前，就对所有的组件进行遍历，将那些不在缓存列表中的组件通过 ``v-if`` 来结束生命周期，这样就达到了优化性能的效果。

#### 关于页面组件的设计
我的设计思路是所有页面组件都是同一个页面组件，但是在加载的时候会向其传一个id，子组件接收到这个id就会根据这个id来渲染相应的界面。


### 功能实现代码
这里将代码贴出来：
```js
<template xlang="wxml">
  <view class="content">
    <view class="tabCard">
      <!-- 使用scroll-view的时候需要注意，需要将样式设置为inline-block以及不换行white-space: nowrap -->
      <!-- 并且不能使用flex布局 -->
      <!-- 或者还有一种处理方式就是通过里面再加一个容器来设置flex布局。 -->
      <scroll-view
        scroll-x="true"
        :scroll-left="scrollLeft"
        scroll-with-animation="true"
        class="tabCardHead"
      >
        <view
          class="scrollConten"
          ref="scrollConten"
          v-for="(item,index) in pageList"
          :key="item.itemID"
        >
          <view class="swichBT" @tap="toPage(index)" :class="{active:item.active}">{{item.title}}</view>
        </view>
      </scroll-view>

      <view class="tabCardBody">
        <swiper
          @change="swChange"
          @animationfinish="animationfinish"
          :current="curIndex"
          :duration="duration"
        >
          <swiper-item :item-id="item.itemID" v-for="(item, index) in pageList" :key="index">
            <view class="loading" v-show="item.loadState">正在加载中</view>
            <testListPage
              class="testListPage"
              @loadComplete="toggleDisplay"
              :itemIndex="index"
              :itemID="item.itemID"
              v-if="item.load"
              v-show="!item.loadState"
            ></testListPage>
          </swiper-item>
        </swiper>
      </view>
    </view>
  </view>
</template> 


<script>
// 实现异步加载组件
const testListPage = resolve => {
  require(["./testListPage"], resolve).then(function(res) {
    console.log("组件加载成功");
  });
};

export default {
  data() {
    return {
      title: "Hello",
      flag: true,
      curIndex: 0,
      cacheList: ["test1"],
      duration: "500",
      scrollLeft: "0",

      //数组对象解释说明：
      // title表示是哪一栏，
      // active表示头部是否是激活状态，
      // load表示是否加载了该组件
      //loadState用来控制是否显示正在加载的图片，因为组件加载与加载中的图片有重叠时间，所以需要单独用一个变量来控制
      pageList: [
        {
          title: "测试一",
          itemID: "test1",
          active: true,
          load: true,
          loadState: true
        },
        {
          title: "测试二",
          itemID: "test2",
          active: false,
          load: false,
          loadState: true
        },
        {
          title: "测试三",
          itemID: "test3",
          active: false,
          load: false,
          loadState: true
        },
        {
          title: "测试四",
          itemID: "test4",
          active: false,
          load: false,
          loadState: true
        },
        {
          title: "测试五",
          itemID: "test5",
          active: false,
          load: false,
          loadState: true
        },
        {
          title: "测试六",
          itemID: "test6",
          active: false,
          load: false,
          loadState: true
        }
      ]
    };
  },
  methods: {
    swChange(e) {
      this.curIndex = e.detail.current;

      // 切换到页面加载相应组件
      this.pageList[e.detail.current].load = true;

      //将头部设为激活状态
      this.pageList.forEach((item, index) => {
        item.active = false;
      });
      this.pageList[e.detail.current].active = true;

      // 控制缓存的组件
      //控制被缓存的列表
      if (!this.cacheList.includes(e.detail.currentItemId)) {
        if (this.cacheList.length < 3) {
          this.cacheList.push(e.detail.currentItemId);
        } else {
          this.cacheList.push(e.detail.currentItemId);
          this.cacheList.shift();
        }
      }
      // console.log(this.cacheList);

      //删除不在缓存范围内的组件
      this.pageList.forEach((item, index) => {
        if (!this.cacheList.includes(item.itemID)) {
          // console.log(item.itemID);
          item.load = false;
          item.loadState = true;
        }
      });

      // 将头部一直保持居中
      // console.log(this.$refs.scrollConten[0].$el.clientWidth);
      const scrollLeft =
        (this.curIndex - 2) * this.$refs.scrollConten[0].$el.clientWidth;
      this.scrollLeft = String(scrollLeft);
      // console.log(scrollLeft);
    },
    toPage(index) {
      //切换class状态
      this.pageList.forEach((item, index) => {
        item.active = false;
      });
      // 切换页面组件
      this.pageList[index].active = true;
      this.curIndex = index;
      //暂时关闭动画
      this.duration = "0";
      // console.log(this.duration);
    },
    toggleDisplay(index) {
      this.pageList[index].loadState = false;
    },
    animationfinish() {
      this.duration = "500";
    }
  },
  components: {
    testListPage
  }
};
</script>
<style lang="scss" scoped>
.active {
  background-color: orange;
}

.content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;

  .testListPage {
    text-align: center;
    width: 100%;
    height: 200rpx;
    background-color: red;
  }
  .tabCardHead {
    width: 750rpx;
    white-space: nowrap;
    display: inline-block;
    position: absolute;
    top: 0;
    left: 0;

    .scrollConten {
      width: 160rpx;
      background-color: aquamarine;
      display: inline-block;
      padding: 10rpx 0;

      .swichBT {
        text-align: center;
        font-size: 28rpx;
      }
    }
  }

  .tabCardBody {
    /* #ifdef H5 */
    margin-top: 70rpx;
    /* #endif */

    swiper {
      // swiper组件必须设置宽度，不能依靠里面的组件来支撑起来
      //并且有默认的高度是300rpx
      width: 750rpx;
      height: 400rpx;
      swiper-item {
        // width: 750rpx;

        .loading {
          text-align: center;
          width: 100%;
          height: 200rpx;
          background-color: antiquewhite;
        }
      }
    }
  }
}
</style>
```

### 总结
这是我自己思考的实现长列表的一个思路，所以有些地方可能处理的不是很好，有更好的方案欢迎提出。
