---
title: JS中sort方法的使用
date: 2019-07-13 12:09:41
tags:
 - JS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们都知道数组有一个用来排序的方法 sort ，这个方法很有用，免去了我们自己排序的方法，并且这个方法在不同种情况下采用的排序算法也不一样，有着不错的性能，这次来了解一下它的具体用法。
<!-- more -->
### 直接使用sort方法
sort方法可以不传递参数直接使用：
```
    {
      const arr = [1, 23, 4, 5, 122, 223];
      console.log(arr.sort());//结果：[1, 122, 223, 23, 4, 5]
    }
```
+ 可以看出在不传递参数的情况下直接使用sort方法对一个纯数字的数组来说不能得到我们想要的结果。

+ 原因是因为如果不传递参数，那么在执行sort方法的时候会自动调用 toString 方法将数组中的每个元素都转化为字符串，然后再进行排序。

+ 我们都知道字符串之间的比较实际上比较的是它们的unicode码值，所以就会出现上面的这种结果。

### 接收参数进行排序
sort方法可以接受一个函数作为参数：
```
    const arr = [1, 2, 3, 3, 52345, 1, 50, 230];
    compare = (value1, value2) => {
      switch (true) {
        case value1 < value2: return -1; break;
        case value1 = value2: return 0; break;
        case value1 > value2: return 1; break;
      }
    }
    console.log(arr.sort(compare));//结果：[1, 1, 2, 3, 3, 50, 230, 52345]
```
+ 比较函数接收两个参数，如果想要第一个参数位置位于第二个参数之前，那么就需要比较函数的返回值是一个负数

+ 如果想要第一个参数位置位于第二个参数之后，这需要比较函数的返回值是一个正数

+ 如果想要两个参数相等，则需要比较函数的返回值是0；

通过传递一个比较函数作为参数就不会自动的将数组中的元素转化为字符串了，并且通过相应设置就能很轻松的获取到我们想要的结果。

### 数组中只有数字
当数组中只有数字的时候可以将将排序作为sort方法参数的比较函数简化：
```
    const arr = [1, 2, 3, 3, 52345, 1, 50, 230];
    compare2 = (value1, value2) => {
      return value1 - value2;
    }
    console.log(arr.sort(compare2));//结果：[1, 1, 2, 3, 3, 50, 230, 52345]
```

可以看出简化后的函数十分的简单，但是只能用于对数字进行排序的情况，根据实际情况选择使用。

### 一个小例子
通过sort排序来实现以下我们经常见到的表格中内容的排序：

+ CSS:
```css
  table {
    border-collapse: collapse;
    margin: 20px auto;
  }

  td,
  th {
    border: 1px solid black;
    padding: 15px;
  }

  #name {
    margin-left: 630px;
  }
```

+ HTML:
```html
<body>
  <button id="name">按姓名</button>
  <button id="age">按年龄</button>
  <button id="money">按财富</button>
  <table>
    <thead>
      <tr>
        <th>姓名</th>
        <th>年龄</th>
        <th>财富</th>
      </tr>
    </thead>
    <tbody>
    </tbody>
  </table>
```

+ JS:
```js
    //一个存放数据的数组
    let info = [
      { 'name': 'csz', 'age': '18', 'money': 99999999999 },
      { 'name': 'csz2', 'age': '19', 'money': 9399999999999 },
      { 'name': 'csz3', 'age': '17', 'money': 9239999999999 },
      { 'name': 'csz4', 'age': '16', 'money': 9935999999999 },
      { 'name': 'csz5', 'age': '41', 'money': 99991599399999 },
    ];
    //复制一份，保存原始的没有规律的数据
    let info2 = [...info];
    //变回最原始的状态
    byNormal = () => {
      info2 = [...info];
    };
    //按照年龄升序
    byAge = () => {
      info2.sort((value1, value2) => {
        return parseInt(value1.age) - parseInt(value2.age);
      });
    };
    //按照姓名升序
    byName = () => {
      info2 = [...info];
      info2.sort();
    };
    //按照财富升序
    byMoney = () => {
      info2.sort((value1, value2) => {
        return value1.money - value2.money;
      });
    };
    //将数组中的数据渲染到页面中
    toHtml = (type) => {
      switch (type) {
        case name:
          byName();
          break;
        case age:
          byAge();
          break;
        case money:
          byMoney();
          break;
        default:
          byNormal();
      }
      let str = '';
      for (item of info2) {
        str += `
      <tr>
        <td>${item.name}</td>
        <td>${item.age}</td>
        <td>${item.money}</td>
      </tr>
        `
      }
      document.querySelector('tbody').innerHTML = str;
    }
    toHtml();
    // 为三个按钮分别绑定渲染的功能
    document.querySelector('#name').onclick = function () { toHtml(name) };
    document.querySelector('#age').onclick = function () { toHtml(age) };
    document.querySelector('#money').onclick = function () { toHtml(money) };
```

>这里实现的都是升序，降序的做法更加的简单，只需要将升序结果的数组调用以下reserve方法就可以了。

<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/AewM4ovvv7uA0F.svwqvQcepoctT*sbnJQXK8NthqkM!/b/dLgAAAAAAAAA&bo=NgJWAgAAAAADB0I!&rf=viewer_4">

### 各浏览器中的实现
+ Mozilla/Firefox : 归并排序（<a href="https://dxr.mozilla.org/seamonkey/source/js/src/jsarray.c#2097ps://en.cppreference.com/w/cpp/algorithm/qsort">jsarray.c 源码</a>）

+ V8 ：数组长度小于一定值时用插入排序，其它用快速排序（<a href="https://github.com/v8/v8/blob/ad82a40509c5b5b4680d4299c8f08d6c6d31af3c/src/js/array.js">array.js 源码</a> 第710行）

+ Webkit ：底层实现用了 C++ 库中的 qsort() 方法（<a href="https://en.cppreference.com/w/cpp/algorithm/qsort">JSArray.cpp 源码</a>）

### 总结
今天在看高级编程的时候看到相关知识，然后自己尝试了一下，再分享记录一下，也可以通过各个浏览器的解释器的代码学习一下排序的知识。