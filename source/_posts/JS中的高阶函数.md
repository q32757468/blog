---
title: JS中的高阶函数
date: 2019-07-16 14:32:27
tags:
 - JS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数是一门语言的重要组成部分，并且js中的函数还有很多特别的地方，比如函数是第一公民，那么这次来了解一下什么是高阶函数。
<!-- more -->
### 什么是高阶函数
高阶函数的概念很简单，指的就是一个函数将另一个函数作为参数，那么这个函数就是高阶函数。
一个简单的高阶函数：
```
    function sum(x, y, fn) {
      return fn(x) + fn(y);
    }
    function add1(num) {
      return num += 1;
    }
    console.log(sum(2, 3, add1));
```
这就是一个简单的高阶函数，将一个函数作为另一个函数的参数。

### JS中自带的高阶函数
+ map/reduce

+ filter

+ sort

+ 。。。还有其他很多的方法都是高阶函数

以上这些我们经常用的方法其实都是高阶函数，因为我们每次在使用时都需要传递一个函数作为参数。

### 自己写一个Map函数
既然我们现在知道了什么是高阶函数，那么我们就可以自己写一个与js中自带的高阶函数类似的高阶函数出来，这里我们自己写一个类似map方法的高阶函数出来：

```
    Array.prototype.myMap = myMap;
    function myMap(fn) {
      let arr = [];
      for (let i = 0; i < this.length; i++) {
        arr.push(fn(this[i], i))
      }
      return arr;
    }
    const arr2 = [1, 23, 3, 45, 6];
    const res = arr2.myMap(function (item, index) {
      return item + index;
    })
    console.log(res);//[1, 24, 5, 48, 10]
```
这样就实现了一个我们自己的map方法，在效果方面与js中自带的方法几乎没有差别，实现js中其他的高阶函数的做法和这个类似。

### 总结
通过了解掌握高阶函数对于理解js中的函数很有帮助，并且学会使用高阶函数在以后的开发中也会很有帮助。


