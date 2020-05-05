---
title: 通过vue来做一个可以简单操作的页面
date: 2019-06-17 16:42:47
tags:
 - VUE
 - JS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;经过学习了vue的一些简单基本的东西，现在讲前面学过的这些东西简单的引用一下，做一个可以简单操作页面元素的页面，同时来熟悉vue的操作。
<!-- more -->
### 代码和效果图
html:
```html
<div id="app">
        <div class="container">
            <div class="card">
                <div class="card-header">
                    相关操作
                </div>
                <div class="card-body">
                    <!-- 给表单添加v-model来实现数据的双向绑定 -->
                    <form class="form-inline">
                        <label for="">id：</label>
                        <input class="form-control mr-md-2 " type="text" name="" v-model="id">
                        <label for="">name：</label>
                        <input class="form-control" type="text" name="" v-model="name">
                        <button class="btn btn-primary ml-md-1 mr-md-3" type="button" @click='add'>添加</button>
                        <!-- 实现搜索功能 -->
                        <label for="">输入关键字进行搜索：</label>
                        <input class="form-control" type="text" name="">
                    </form>
                </div>
            </div>
            <table class="table table-light mt-5 table-bordered table-striped">
                <thead class="thead-light">
                    <tr>
                        <th>Id</th>
                        <th>Name</th>
                        <th>Time</th>
                        <th>opration</th>
                    </tr>
                </thead>
                <tbody>
                    <!-- 通过v-for的循环来将我们的数据来渲染到页面中 -->
                    <tr v-for="(item) in info" :key="item.id">
                        <td>{{item.id}}</td>
                        <td>{{item.name}}</td>
                        <td>{{item.time}}</td>
                        <td><a href="" @click.prevent='rm(item.id)'>删除</a></td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
```

js:
```js
            var vm = new Vue({
            el: '#app',
            data() {
                return {
                    id: '',
                    name: '',
                    time: '',
                    keywords:'',
                    // 将我们页面中的数据放在这俩面
                    info: [{
                            id: '1',
                            name: 'aaa',
                            time: new Date()
                        },
                        {
                            id: '2',
                            name: 'bbb',
                            time: new Date()
                        },
                    ],

                }
            },
            methods: {
                // 通过自己构建函数来控制添加和删除
                add() {//这是添加的函数
                    this.info.push({
                        id: this.id,
                        name: this.name,
                        time: new Date(),
                    });
                    this.id = this.name = '';
                },
                rm(id) {//这是删除的函数，我用了两种写法
                    // this.info.forEach((item,index) => {
                    //     if(id=item.id){
                    //         this.info.splice(index,1)
                    //         return ;
                    //     }                  
                    // });

                    // 这一种代码会更简便一些
                    this.info.splice(this.info.findIndex(item => {
                        return item.id == id;
                    }), 1);
                },
                searchList(keywords){
                    return this.info.filter(item => {
                        return item.name.includes(keywords);
                    })
                }
            },
        })
```

最后的效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/oTLF7.aDPoIaR6KZSqfT7Y7ICMF43yoVYspA5rV52MM!/b/dL4AAAAAAAAA&bo=CgW3AQAAAAADB5s!&rf=viewer_4">

通过页面上的增加和删除就可以实现页面的修改

### 实现原理
在vue中更看重于业务逻辑，所以很多方面的工作我们不需要去关心，也不需要去操作dom对象就可以轻松的修改页面上的内容

* 我们通过``v-for``的循环将我们的数据渲染到页面中

* 我们通过``v-model``数据双向绑定来将用户在页面中输入的数据放到我们存放数据的地方，因为vue的特性，只要数据有更新，那么页面就会跟着一起更新，这是十分方便的一件事情

* 再通过数组的一些操作方法将我们想要删除的数据进行删除，那么页面中相应的内容也会被删除。

* 搜索功能是通过``v-model``数据的双向绑定来拿到用户需要搜索的数据，然后通过filter()这个方法来设置条件进行过滤，然后返回一个新的数组，过滤的条件可以通过inclues来设置。

### 学习到的一些知识
正如我上一篇博客介绍的那样，在特定的情况下使用filter之类的方法会更加的便捷，这次在写搜索的那个函数的时候我就用到了filter这个方法，使代码更加的精简，这里可以使用foreach，但这会有更加多的代码量，所以我没有这么做。

### 我遇到的一些坑与问题
* 第一个花费时间比较久的地方是当我完成了数据的添加的这个功能之后发现了一个问题就是当我向页面中添加完数据之后，我再修改输入框中的数据，已经渲染在页面之中的数据也会跟着一起修改，很显然这不是我想要的效果，后面发现是我将一个带着变量的对象直接push到数组当中，而不是在单击事件的时候通过变量创建一个新的对象再push进去。**也就是说应该需要一个中间的变量来将一个会实时变化的变量变成一个固定的变量。**

* 第二个问题就是在使用findIndex方法的时候将``return item.id == id``写成了``return item.id = id`` 导致在操作的时候出现了问题

### 总结
这次写这些东西让我对vue有了更加清晰的概念，并且也用了一些以前没有用过的方法来使代码更加的简单，同时要记住写代码犯的一些错误，要记住在进行比较的时候判断是否相等要用``==``，千万不能永成赋值符号``=``

