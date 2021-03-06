---
title: 理解JS中的变量对象
date: 2019-11-17 14:12:18
tags:
 - JS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;之前有提到过JS中的执行环境(执行上下文)是JS中非常重要的一个概念，而前面也提到了执行环境当中主要包含了三部分：变量对象、作用域链、this指向，这里这里就承接前面，来更为详细的介绍一下变量对象究竟是什么。
<!-- more -->
### 变量什么时候被的创建
变量对象的创建时机非常好理解：

当前函数**被调用但还未执行**之时变量对象就在当前执行环境中被创建了。

还未执行就意味着并未开始执行当前函数中的代码。

### 变量对象创建的过程

1. 创建 **arguments** 对象
该对象的属性名是类似数组中的索引的值：'0'、'1'、'2'.....，属性值是传入的实际参数，此外还有 length 属性表示传入的实际参数的个数，所以说函数中的 arguments 是一个伪数组。

2. 找到所有的**函数声明**
找到函数内所有通过 function 关键字声明的函数，并将其保存在变量对象中，属性名是 函数名 ，属性值是函数的引用地址， 同名则后面的覆盖前面的。这就是为什么JS中存在函数提升。

3. 找到所有的**变量声明**
找到函数内所有的变量声明，并且将其保存在变量对象中，属性名是 变量名 ，属性值为 **undefined** 。这就解释了为什么访问一个未赋值的变量时返回的是undefined 了 ，以及为什么用 var 声明会存在变量提升了。

    但是我们都知道 ES6 新增的 let 和 const 是不存在 变量提升的，那么它们会不会被保存进变量对象中呢？
    + 我们可以通过测试来体会一下，首先是声明但不赋值,在声明后使用：
    ```js
        let a;
        console.log(a);
    ```
    结果：
    <img src="http://m.qpic.cn/psb?/V131x4904WMIoW/Vu6gV1Td2WaxipoMlZ1h2RlOcJrnA8Nakzs6C6.O9WA!/b/dL8AAAAAAAAA&bo=zgAiAAAAAAADB84!&rf=viewer_4">
    可以看到结果为 undefined ，这也就意味着通过 let 或者是 const 声明的变量也会被保存进变量对象当中。

    + 那么为什么 let 和 const 不支持变量提升呢：
    同样的测试，这次在声明之前就使用变量：
    ```js
        console.log(a);
        let a;
    ```
    结果：
    <img src="http://m.qpic.cn/psb?/V131x4904WMIoW/HJiG3aBIXl0FkFaw2*o.U3k2fVk559YTQrjTJX**V00!/b/dFQBAAAAAAAA&bo=sgI4AAAAAAADB6o!&rf=viewer_4">
    结果为报错'不能在声明之前使用'，这也就是我们场常说的暂时性死区。

    + **最后结论，ES6中的 let 和 const 会存在暂时性死区的问题，不能在声明之前使用，但是一样的会被保存进变量对象当中。**


### 变量对象与活动对象
变量对象与活动对象基本其实是一个对象，主要是**在不同的时间叫法不同**而已：

变量对象是函数被调用但**未执行**时在创建在执行上下文中的，此时叫做**变量对象**，此时变量对象中的所有**属性都不能够被访问**（除了全局执行环境外）。

而活动对象则意味着当前函数**开始执行**了，此时变量对象就被转化为**活动对象**了，转化之后所有的**属性就可以访问**了。

### 总结
理解了变量对象这个概念在使用js的过程中相信会有更清楚的认识，不了解执行环境的小伙伴可以参考我前面的<a href="https://q32757468.github.io/2019/07/21/%E7%90%86%E8%A7%A3JS%E4%B8%AD%E7%9A%84%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83/">博客</a>。关于执行环境的理解。

### 参考
<a href="https://www.jianshu.com/p/f8e628b5c312">彻底理解js的执行上下文，以及变量对象</a>