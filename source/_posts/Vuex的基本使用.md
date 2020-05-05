---
title: Vuex的基本使用
date: 2019-09-11 16:49:32
tags:
 - JS
 - VUE
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vuex作为vue全家桶之一，使用vue开发的过程中有着无可替代的作用，可以解决多个组件中传值的问题，并且本身的使用也非常的简单，有着非常强大的作用。我把它理解为一个数据共享中心，所有的组件都共享这里面的数据，并且都可以对其中的数据进行修改。来看看它是如何发挥作用的。
<!-- more -->
### 安装
首先是安装vuex，可以通过npm来安装vuex，注意，因为是生产所需要的依赖，所以需要``-S``选项：
```shell
npm install vuex -S
```
当然直接从<a href="https://vuex.vuejs.org/zh/installation.html">官网</a>下载然后引用也是可以的。

### 用法
首先需要在项目目录下创建一个名为 **store.js** 的文件，这个文件就是我们vuex的主要文件，如果是在项目比较大的情况下，可以将这个文件中的各个部分单独拆分成一个文件，然后将所有的文件放在名为store的文件夹内。


### store.js
来看看**store.js**这个文件的基本内容及作用：
```js
//正常的引入
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  // 通过state对象来存储数据，然后可以在组件中通过this.$store.state来获取我们想要的数据
  state: {
    count: 1000
  },

  // getters对象，这个对象和计算属性非常的像，用法也非常的像
  getters: {
    countAdd1(state) {
      return state.count + 1
      }
    },

  //mutations是修改数据的地方，只有这里可以修改修改state中的数据
  // 通过mutations就像是注册了一个事件一样，但是并没有将事件应用起来，还需要通过commit来将其进行提交
  mutations: {
    add1(state) {//可以接收第二个参数作为这个函数的额外参数，最好是用一个对象来定义
      state.count++
    },
    redu1(state) {
      state.count--
    }
  },

  // actions可以通过提交mutations来变更状态并且支持异步的操作
  //actions接收一个和当前store实例具有相同的方法和属性的 context 对象，所以可以通过 context.commit 来提交一个 mutations
  actions: {
    add1(context) {//同样，在分发的时候也可以接收额外的参数
      context.commit('add1');
    },
    redu1(context) {
      context.commit('redu1');
    },
  },
  
})

```
通过这样就相当于创建了一个数据的共享中心，然后就可以在各个组件中使用其中的数据了

### 在组件中使用
还是以这个文件为例子，来看看如何使用其中的数据：

#### 在组件中直接使用了state中的数据：
store.js：
```js
  state: {
    count: 1000
  },
```

APP.vue:
```js
      <p>这是count的数值：{{this.$store.state.count}}</p>
```
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/MOPKs8cQWsBGqhcFPp9FBaS4PJmKzKX2jdjDWGHzkN0!/b/dL4AAAAAAAAA&bo=FAEvAAAAAAADBxg!&rf=viewer_4">
可以看到，成功获取了我们想要的数值。

#### 通过 **getters**获得计算后的值：
store.js：
```js
  getters: {
    countAdd1(state) {
      return state.count + 1
    }
  },
```
c1.vue:
```html
    <p>这是getters count+1后的数值：{{this.$store.getters.countAdd1}}</p>
```
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/FO1*dV28OJjel4VR6FqH7KJVf6jBdmrgJDA540jvBTo!/b/dL4AAAAAAAAA&bo=sgFBAAAAAAADB9A!&rf=viewer_4">
可以看出成功获取了通过getters计算后的值，并且我这里是在另一个组件中使用的数据，从这里也可以看出vuex管理下的数据共同是十分方便的。

#### 通过 mutations 来修改数据
前面的注释中也提到过 mutations 是修改数据的唯一途径，所有要修改state中数据的操作都必须在 **mutations**完成:

store.js：
```js
  mutations: {
    add1(state) {//可以接收第二个参数作为这个函数的额外参数，最好是用一个对象来定义
      state.count++
    },
    redu1(state) {
      state.count--
    }
  },
```

APP.vue:
```html
      <button class="add" @click="add1">+</button>
      <button class="redu" @click="redu1">-</button>
```
```js
  methods: {
    add1() {//可以接收额外的参数，推荐使用对象来传递多个参数
      this.$store.commit("add1");
    },
    redu1() {
      this.$store.commit("redu1");
    }
  }
```
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/Wr3laexW93nJPZyimOu.efGUQ1HBnZnuuY8cikitiBI!/b/dL8AAAAAAAAA&bo=cQFfAAAAAAADBw0!&rf=viewer_4">

可以看出可以通过 **mutations** 来对state中的数据进行修改，不过要使其生效必须要通过 **commit**使其生效，可以看出我这里使通过在组件中的methods中通过两个方法来进行提交。

**关于mutations要注意的几点：**
+ mutations相当于是注册了事件一样，要生效需要提交，然后调用相应的方法

+ mutations 中不能写异步的任务，这点要十分的注意

+ mutations 中定义的方法可以接收额外的参数，并且推荐是使用对象来传递额外的多个参数，在通过**commit**进行提交的时候可以传递相应的参数，我这里没有使用，但是类似与这样：
```js
this.$store.commit("add1",{count2:999});
```

#### action 的使用
**Action** 的作用与 mutations 类似，不过区别就是 Action 提交的是 mutations，而不是直接修改state中的数据，并且最终重要的是在**Action 中可以进行异步的操作**，这是action的使用：
store.js：
```js
  //actions接收一个和当前store实例具有相同的方法和属性的 context 对象，所以可以通过 context.commit 来提交一个 mutations
  actions: {
    add1(context) {//同样，在分发的时候也可以接收额外的参数
      context.commit('add1');
    },
    redu1(context) {
      context.commit('redu1');
    },
  }
```
APP.vue:
```html
      <button class="add" @click="add1">+</button>
      <button class="redu" @click="redu1">-</button>
```
```js
  methods: {
    add1() {
      this.$store.dispatch("add1");
    },
    redu1() {
      this.$store.dispatch("redu1");
    }
  }
```
这里是就没有通过 mutations 的方式来进行提交，而是通过 action 来提交 mutations ，然后通过在组件中分发 action 来实现对数据进行修改 ，这种方式实现的最终效果和上面的 mutations 演示所实现的效果是一样的，不过最大的区别就是在 action中提交 mutations 的时候是可以进行异步操作的。

### 使用 mutations 和 action 来进行修改数据的区别
这里之所以单独提出来将是因为刚开始的时候不太清楚这两个究竟是怎么回事，现在弄明白了，所以单独详细的讲一下：

+ 首先就是使用步骤方面的区别，在 mutations 中定义的方法只需要在组件中通过 commit 提交就可以使用了，如果是使用 action 的话则需要先在 mutations 中定义修改数据的方法，然后在 action 中再定义方法提交 mutations 中的方法，最后需要使用的话则需要在组件中通过 dispatch 来分发 action 中定义的方法使修改生效。

+ 再一个就是定义在 mutations 的方法不能写异步的任务，但是写在 action 中的方法则可以写异步的任务。

+ 这里提一下 commit 和 dispatch 的区别，简单来说 commit是用来提交 mutations 中的方法的， dispatch 是用来提交 action 中的方法的，前者不支持异步，后者支持异步。

在不需要异步任务的情况下直接使用 mutations 和使用 action的结果没有什么区别，主要区别就是在需要异步的情况下，推荐是无论什么情况都使用action来提交。

### 使用 mapState 、mapGetters 、 mapMutations
在官方文档中者三个方法被称为辅助函数，这样理解是没有什么问题的，因为作用其实也就是使我们使用vux更加的简便，拿来看看他们究竟能起到怎么样的作用：

  如果我们**不使用这些辅助函数**，那么我们经常要很繁琐的书写一些代码来使用vuex，就像是这样：
  ```html
      <p>这是getters count+1后的数值：{{this.$store.getters.countAdd1}}</p>
  ```
  可以看出这里通过``this.$store.getters``这么长的代码来获取数据是非常不方便的一件事情，所以就需要**使用辅助函数**,辅助函数的使用也是非常的简单，我们一个个来看一下（一样的还是基于前面的例子）。

#### mapState 辅助函数
使用之前需要将其引入：
```js
import { mapState } from "vuex";
```

在 ``computed``中使用：
```js
  computed: {
    //这里使用了扩展运算符，将mapState返回的对象扩展到computed对象当中，以保证不会影响 computed 原本的使用。
    ...mapState({
      // 第一种方式，使用箭头函数，通过mapState将state中的count映射为此组件date中的count
      count: state => state.count

      // 更加简便的一种写法，与上面的作用是相同的
      // count: "count",

      // 因为键值都是一样的，所以还可以写的更加的简单
      // count,

      // 为了不改变this的指向，必须用常规函数
      // count(state) {
      //   return state.count + this.localCount;
      // }
    })
  }
```
这里这几种写法的结果都是一样的，可以根据情况进行选择，这样使用之后的结果都是一样的。

之后就可以像使用data中的数据一样使用原本state中的数据了:
```html
      <p>这是count的数值：{{count}}</p>
```
当页面中重复使用到同一数据的时候就可以提供很大的便利。

#### mapState 辅助函数
使用之前需要将其引入：
```js
import { mapGetters } from "vuex";
```

同样也是在``computed``中使用，使用起来与 mapState 类似：
```js
  computed: {
    ...mapGetters(["countAdd1"]) //这里是使用的数组的形式，也可以使用对来来进行重命名
  }
```

之后使用起来也就更加的简便了：
```html
    <p>这是getters count+1后的数值：{{countAdd1}}很棒！</p>
```

#### mapMutations  辅助函数
当我们在组件中对 mutations 进行提交的时候就可以使用 mapMutations 来进行简化。

同样使用前需进行引入：
```js
import { mapMutations } from 'vuex'
```
这里引用了官网的例子：
```js
export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```

之后我们在组件中使用的时候就会变得更加简便：
```html
      <button class="add" @click="increment">+</button>
```

** 在大量重复使用同一数据的情况下使用辅助函数可以提供便利，所以根据情况合理使用吧！**


### 总结
vuex是一个简单使用的工具，但是功能很强大，可以解决很多的问题，在适当的场景适当使用可以发挥很强作用。



