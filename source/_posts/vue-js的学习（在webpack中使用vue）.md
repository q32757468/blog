---
title: vue-js的学习（在webpack中使用vue）
date: 2019-07-28 17:09:12
tags:
 - webpack
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前面学习过了vue的一些基础的知识，但是都是直接开发，并没有使用webpack这样的打包工具，那么该如何通过webpack来开发一个vue项目呢，并且这样与直接开发有什么不一样的地方呢，就让我们来学习一下。
<!-- more -->
### 在项目中安装vue
在学习使用webpack之前我们在项目中都是直接使用 script 标签在项目中引入vue所需要的文件，那么现在通过webpack来构建项目的话就应该将所有的依赖都引入到入口文件当中，页面中只会引入一个入口文件。

#### 通过npm来安装vue
1. 先将vue安装进项目：
```
npm i vue -S
```

2. 然后在入口文件中引入vue
```
import Vue from 'vue'
```

通过这样简单的安装引入就成功的将vue引入了项目当中

#### 通过 .vue 文件来设置组件
当我们执行完上面的引入操作，我们直接在入口文件中写一些相关的vue中的代码的话，会在浏览器的控制台中看见报错信息，意思大概就是我们引入的vue包是runtime的包，不能直接使用组件相关的东西。

那我们要怎么样做呢？要解决这个问题只需要引入完整的vue包文件就好了，有三种方式：

+ 找到 **node_modules**中的vue项目包，然后修改它的 package.json ，将它提供给外部的入口文件修改为完整的那个：
```
"main": "dist/vue.js",
```

+ 在项目的入口文件中修改引入的vue文件：
```
import Vue from 'vue/dist/vue'
```
>值得注意的一件事情是这里的路径可以将前面的node_modules这个目录省略掉，会自动去这个目录中找我们提供的这个路径。

+ 修改webpack的配置文件，加上以下代码：
```
resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js'
    }
  }
```

那么任选这三种办法中的其中一种都可以解决前面的问题，但是实际上并不推荐这样去做，因为在webpack中使用vue比较推荐另一种做法，就是使用**render**函数，通过 render 函数来引入vue组件，而这种组件是单独的以``.vue``后缀结尾的文件。

##### 安装相关包和进行相关配置


1. 将相应的工具安装进项目
```
npm i vue-loader vue-template-compiler -D
```

2. 在webpack配置文件中配置好相应的匹配规则：
```
    rules: [ // 匹配规则
      { test: /\.vue$/, use: 'vue-loader' },//处理vue文件
    ]
```

3. 在webpack配置文件中配置好插件相应设置：
```
  plugins: [
    // 在webpack的4.x的版本中必须引入这个配置
    new VueLoaderPlugin()
  ]
```

通过以上的安装和配置，一个基本的环境就配置好了，接下来来看看如何通过``.vue``文件来创建组件。

### 使用render函数
新建一个``.vue``文件，这个文件将会替代页面中一一开始的id为app的元素：
```html
//定义模板
<template>
  <div>
    这是一个测试内容
    <p v-text="msg"></p>
  </div>
</template>

// 定义业务逻辑
<script>
export default {
  //通过export default来向外暴露
  data() {
    return {
      msg: "这是一条消息"
    };
  }
};
</script> 

//定义样式
<style>
</style>
```

使用**render**函数引入前面定义的vue文件：
```js
//先将文件引入页面当中
import App from './App.vue'
const vm = new Vue({
  el: '#app',
  //再使用render函数使用到引入的组件
  render: c => c(App),//这里是使用了ES6中的箭头函数的语法进行了简写
  data: {
    msg: '这是一个测试内容'
  },
  methods: {
  }
})
```

这是render函数的完整写法：
```js
render: function (createElements) {
  return createElements(App)
}
```

经过这些操作以后页面中原本的 #app 的div块就会被替换为 引入的 App组件，所以要注意的是不要在里面写任何东西，因为会被替换掉。以后需要在其中引入其他的组件就直接在App组件中引入就可以了。

### 在webpack中使用vue-router
前面已经学习过了如何使用vue-router，其实在webpack中使用vue-router的方式还是差不多的，只不过最主要的区别就是引入包和引入各个组件的时候有区别了。

#### 安装
```
npm i vue-router -S
```

#### 引入
```
import Vue from 'vue'
import VueRouter from 'vue-router'
```

#### 使用vue加载
```
Vue.use(VueRouter)
```

#### 一个简单的小例子：
除了前面的安装方面的区别之外，其他的几乎没有什么区别了，要是有区别的话也只是在组件的定义方面存在区别，来看一个简单的小例子：

+ 这是网页文件：
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <link rel="icon" href="data:image/ico;base64,aWNv">
  <title>Document</title>
</head>

<body>
  <div id="app"></div>
</body>

</html>
```

+ 这是入口文件：
```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import App from './App.vue'
//如果涉及到很多的路由组件，可以将这些路由组件单独放到一个js文件当中，然后就只要引入这一个js文件就可以引入所有的路由组件。
import login from './login.vue'
import register from './register.vue'

Vue.use(VueRouter)
const router = new VueRouter({
  routes: [
    { path: '/', redirect: '/login' },
    { path: '/login', component: login },
    { path: '/register', component: register }
  ]
})

const vm = new Vue({
  el: '#app',
  render: c => c(App),
  router,
})
```

+ 这是render的组件：
```js
//定义模板
<template>
  <div>
    <router-link to="/login">登录</router-link>
    <router-link to="/register">注册</router-link>
    <router-view></router-view>
  </div>
</template>

// 定义业务逻辑
<script>
export default {
  //通过export default来向外暴露
  data() {
    return {
      msg: "这是一条消息"
    };
  }
};
</script> 

//定义样式
<style>
</style>
```

+ login组件：
```
<template>
  <div>
    <h1>这是登录组件</h1>
  </div>
</template>

<script>
export default {};
</script>

<style scoped >
div {
  color: beige;
}
</style>

```

+ register组件
```
<template>
  <div>
    <h1>这是注册组件</h1>
  </div>
</template>

<script>
export default {};
</script>

<style scoped>
</style>
```

可以看出用法还是和以前差不多，只不过用来一些新东西而已，每个组件都是一个单独的vue文件。


### 单独的`.vue文件`
前面已经看到了 .vue 是如何使用了，那么现在来详细讲一讲这类文件的一些细节：

+ 可以看出 .vue 文件主要分成三部分，一部分是模板部分，这个是组件的模板，就像是原先直接在html中通过**template**标签定义模板那样，并且也是一样的用法，只不过是换了一个地方使用。

+ 第二个是**script**部分，这个是用来处理业务逻辑的部分，其实就是一个vue的实例的对象，只不过需要通过``export default``将其暴露出去。

+ 第三个部分就是样式部分，这个部分就更好理解了，是用来定义组件的样式的，但是又两点要注意：

  - 可以设置书写样式的语言：
  ```
  <style lang="less">

  </style>
  <style lang="sass">

  </style>
  ```
可以通过这个lang属性来设置语言，如果不设置默认就是css.


  - 可以设置样式的作用域：

  默认是全部生效的作用域，什么意思呢，就是在这种情况下设置的样式会应用到页面中所有符合条件的元素上。

  通常情况下会这样设置：
  ```
  <style scoped>
  </style>
  ```
  加一个``scope``属性就只对当前组件有效。

### 总结
看到这里就明白vue的组件化开发的便利之处了，把网页当成若干个组件，方便维护管理，并且有着很高的可复用性，并且本身就是只关心业务逻辑，使得开发简化，佩服佩服。