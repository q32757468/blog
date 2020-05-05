---
title: vue-js的学习（路由的学习与使用）
date: 2019-07-02 18:07:13
tags:
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这次来学习vue中的路由，那么什么是前端路由，前端路由就是在单页面中通过url地址的hash来进行单页面的跳转的，而且并不会重新请求页面，这就是前端路由，一般都是通过 # 来实现的。这次来具体学习以下vue-router这个库来实现vue中的路由功能。
<!-- more -->

### 如何使用vue-router
#### 首先是要引入vue-router包
那么，其实引入的方式有很多种，可以直接下载到本地引入，也可以使用npm之类的，具体可以看官方网站的介绍<a href="https://router.vuejs.org/zh/installation.html">介绍</a>。

#### 创建一个组件构造器
这里要注意的是这里创建的不是一个组件，而是一个构造器，虽然路由是在不同的组件之间进行切换，但是这里先是创建构造器，也就是通过``Vue.extend``来创建构造器，最后通过路由router将构造器注册成为组件。就像下面这样：

```
var login=Vue.extend({
      template: '<h1>登录组件</h1>'
    });
```

可以看出这就是我们在学习前面的创建组件的时候的创建构造器的方式，所以这个也可以简写：
```
var register={
      template: '<h1>注册组件</h1>'
    };
```
直接用一个对象来表示构造器。而构造其中的模板就是我们之后路由组件中的内容。


#### 通过VueRouter来创建路由实例及定义路由规则
```html
        const router = new VueRouter({      //创建一个路由对象
            routes: [{                      //routes的意思就是道路的意思，这个数组里面就放我们所要定义的路由规则
                                         //这个数组里面用对象来表示每个路由规则，两个对象就是两条路由规则
                    path: '/login',     //对象里面的path表示路由的地址
                    component: login    //component表示对应的路由组件
                },
                {
                    path: '/register',
                    component: register
                }
            ],
        })
```
>这里要注意的是这里的routes是一个数组，写的时候不要写错了。


#### 挂在到vue实例中 
在实例中挂载过来路由规则才会生效

```
var vm = new Vue({
      el: '#app',
      router: router // 使用 router 属性来使用路由规则,可以直接简写成一个router，因为键和值都是一样的。
    });
```

#### 使用 router-link 来对路由组件进行导航
其实可以通过HTML本身自带的 a 标签来设置url地址来设置hash来实现导航跳转的目的，但是在vue-router中也提供了进行导航的方式，并且更加的实用。
就是使用 router-link ：

```
    <div id="app">
        <router-link to="/login">登录</router-link>
        <router-link to="/register">注册</router-link>
    </div>
```
router-link标签默认在页面上是渲染成 a 标签的，同样，这个也可以通过 ``tag``  属性来进行修改。


#### 使用 router-view 将路由组件挂载到页面中
前面都只是创建路由组件，但是创建好了没有放到页面里面去也相当于没有什么用，这里就需要通过 router-view 标签将前面定义过的路由组件放到页面中去。

```html
        <!-- 默认一个 router-view 只会显示一条平级的路由规则 -->
        <router-view></router-view>
```

>要注意的一件事情是一个 router-view 默认只显示当前url地址匹配到的路由组件的内容，如果没有匹配到那么就不会显示出来，比如前面都没有设置根路径的路由那么页面初始的状态下是什么都不会显示的，只有在路由切换到相应的地址的时候才会有显示。

### 设置路由重定向
在很多时候我们需要将一个路由地址可以对应多种路径，比如让上面的页面在根路径下默认显示 login 那么这个时候就需要使用路由重定向来将根路径重定向到 login
```
                {
                    path: '/',
                    //这里要注意的是这里redirect后面跟的是重定向的路径
                    redirect: '/login' 
                }
```
我们只需要给我们需要重定向的路径添加一条路由规则并且给它重定向到我们想要的路径就可以了。

### 设置路由链接激活样式
有时候我们需要给当前所在位置的 router-link 设置高亮，那么这个时候我们就可以通过给 ``router-link-active``这个类设置样式来实现我们想要的效果，因为我们每次切换路由vue每次都会自动的将这个类放到当前所在的 router-link 上，所以只需要修改这个类的样式就可以了。

```
<style>
    .router-link-active {
        color: red;
        font-size: 20px;
        background-color: aqua;
    }
</style>
```

<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/T*wp.OVMRn7cJA0qmNP*P6Z6JqkexmQw0C4wpB85sMg!/b/dDABAAAAAAAA&bo=AQOoAQAAAAADB4k!&rf=viewer_4">

可以看出这个样式成功应用上去了。

当然，这个类的名称是可以自定义修改的，我们只需要在创建路由对象的时候设置 ``linkActiveClass``就可以来全局配置这个链接激活状态的类名。
就像这样：

```js
        const router = new VueRouter({
            // 这里设置链接激活的类
            linkActiveClass: 'myclass',     //设置过后就需要通过修改我们自定义的类来设置链接激活样式了

            // 这里设置路由规则
            routes: [{
                    path: '/',
                    redirect: '/login' 
                }, {
                    path: '/login',
                    component: login
                },
                {
                    path: '/register',
                    component: register
                }
            ],
        })
```


### 设置路由组件切换动时画效果
设置路由组件间的动画切换效果也很简单，只需要将 ``router-view``标签 放进动画的 ``transition``标签里面并且设置动画的两组类（或者直接用第三方的库）就可以了。

```css
<style>
    .myclass {
        color: red;
        font-size: 20px;
        background-color: aqua;
    }

    .v-enter,
    .v-leave-to {
        opacity: 0;
        transform: translateX(150px);
    }

    .v-enter-active,
    .v-leave-active {
        transition: all 1s ease;
    }
</style>
```

```html
        <transition mode="out-in">
            <router-view></router-view>
        </transition>
```

通过这样的设置就可以使组件在切换的时候拥有动画效果了。

### 路由嵌套
假如如有组件中还嵌套有路由,那么要实现一层一层的显示路由就要用到路由嵌套，在需要嵌套的路由规则中通过 ``children`` 来实现路由嵌套。

+ 首先是在一个路由构造器中嵌套有子路由
```html
    <div id="app">
        <router-link to="/login">登录</router-link>
        <router-link to="/register">注册</router-link>
        <transition mode="out-in">
            <router-view></router-view>
        </transition>
    </div>

    <template id="test">
        <div>
            <h1>这是登录组件</h1>
            <router-link to="/login/son">点这里切换到子组件</router-link>
            <router-view></router-view>
        </div>
    </template>
```

+ 然后是定义了子路由,并且将其嵌套在父级的路由规则中
```js
        var son = {
            template: "<h1>这是嵌套在里面的子组件</h1>"
        }

        const router = new VueRouter({
            linkActiveClass: 'myclass',
            routes: [{
                    path: '/',
                    redirect: '/login' //这里要注意的是这里redirect后面跟的是重定向的路径
                }, {
                    path: '/login',
                    component: login,
                    // children是父组件规则中的一个属性的值，并且这是一个数组，这个数组里面放的就是子路由的路径规则，相当于是在一条路由规则里面来定义新的路由规则
                    children: [
                        // 这里一样可以写很多条的子路由规则
                        {
                            path: 'son',  //这里使用的相对路径
                            component: son
                        }
                    ],
                },
                {
                    path: '/register',
                    component: register
                }
            ],
        })
```

>这里要注意的一点是子路由中的路由规则中的路径使用的是相对路径，并且是相对于父级路由的相对路径，如果使用的是从根路径开始的绝对路径，那么就只会显示子路由中的内容，就不会显示父级路由中的内容。

+ 这里也可以看到效果，当我们切换到子路由，显示了从父级路由开始的路由组件：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/6hUG*9YVk7hmAeE59TRW.VwtRkvFbw21GYJYweNhOzo!/b/dL8AAAAAAAAA&bo=dgQMAgAAAAADB14!&rf=viewer_4">


### 路由参数的传递及获取的两种方式

我们经常会有需求在url地址中传递参数，那么在vue中拿到参数以及获取参数的方式有两种：

#### 第一种，通过query
传递参数的方式像是在学php时用get方式传递数据时使用的方法：

```html
        <router-link to="/login?id=1&name=csz">登录</router-link>
```

获取参数的方法：

+ 首先我们要知道在我们创建的vue实例中有一个$route属性，这个属性中就包含了我们的路由信息，通过展开这个属性，我们可以发现有一个 query 属性，而这个属性是一个对象，包含了我们通过查询字符串所传递的参数，我们就可以通过调用这个对象来拿到我们的参数。
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/gRrhsGcI.*0vtKmQ59Vuz4G8xc3xaZR5uigGv20OQs0!/b/dFIBAAAAAAAA&bo=EwQkAQAAAAADBxA!&rf=viewer_4">

+ 通过这种方式我们可以很轻松的拿到我们想要的参数并且使用它
```
        <h1>这是登录组件---{{this.$route.query.id}}</h1>
```
那么此时当我们切换到登录组件的时候就还会额外显示我们传递的id参数了。

#### 第二种，通过params
这种方式与query有很明显的区别：

+ 首先是传递参数的方式有所区别
```
    <router-link to="/register/2/csz2">注册</router-link>
```

    - 可以看出这里在url地址上只设置了参数的值，并没有对参数设置名称。
    - 参数的名称还需要在设置路由路径的时候进行设置：
    ```
            {
                    path: '/register/:id/:name',
                    component: register
            }
    ```

+ 通过这种方式也能实现一样的效果，并且拿到参数：
```
        <h1>这是注册组件---{{this.$route.params.id}}</h1>
```
也可以成功拿到我们想要的值。

### 命名视图
在有些场景我们希望同时展示多个视图，也就是多个 ``<router-view></router-view>`` ，但是默认情况下我们在页面中使用多个 <router-view></router-view> 的话也不会显示多个视图，那么这个时候我们就需要通过对视图来进行命名来显示我们想要的视图了。

#### 首先是对视图进行命名
```html
    <div id="app">
        <router-view></router-view>
        <div class="wrap">
            <router-view name="left"></router-view>
            <router-view name="right"></router-view>
        </div>
    </div>
```
只要对视图进行了命名就可以指定这个视图显示什么路由组件了。

#### 创建构造器以及创建路由实例

```
        var top1 = { //这里还有一个大问题，一个bug，就是组件构造器的名字不能够使用 top 否则直接会找不到这个构造器从而报错
            template: '<h3 class="top1">这是上边部分</h3>'
        }
        var left = {
            template: '<h3 class="left">这是左边部分</h3>'
        }
        var right = {
            template: '<h3 class="right">这是右边部分</h3>'
        }

        const router = new VueRouter({
            routes: [{
                path: '/',
                components: {       //这里使用的是components而不是component
                    default: top1, //没有设置name的默认就是用default
                    right,
                    left,
                }
            }]
        })
```

需要在同一级路径显示多个路由组件就需要在配置这条路径的路由规则的时候使用  ``components`` 来配置显示的路由组件，这里面是一个对象，是 视图  ``name:构造器名字`` 的这种形式，对于没有命名的视图则使用 default 来进行配置。

<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/8yPBUkHvZsoDoT*Gz01W2KTd5fihtXgxL0GqK9iFORU!/b/dL8AAAAAAAAA&bo=gAcSBAAAAAADB7M!&rf=viewer_4">

>这里要注意的是这里因为配置的是多个路由组件，所以使用的是 components 而不是前面只显示单个组件时的 component。

这里我发现一个vue中的bug，起初我在定义三个路由组件的构造器的时候使用了 top left right 这三个名字，最后在页面中查看，只显示了后面两个组件，然后控制台报了一个错误，说有一个组件没有定义，然后我猜想是不是定义top的时候哪里写错了，后面发现并不是，后面我只能猜测难道是因为名字的问题？然后我改了一个名字，果真是名字的问题。这里值得注意以下。


### 总结
这是是对vue router学习的一些笔记，有一些问题需要注意

+ 两种方式都可以传递参数并且获取到参数，但是二者有明显的区别，要注意区分，使用哪种方式看个人习惯就好了，我比较习惯于使用query来传递和获取参数。

+ 在命名视图的时候有一个bug，要多注意，以后对组件命名尽量不要使用 top 这个名字来命名。 