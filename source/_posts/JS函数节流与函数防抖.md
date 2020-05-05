---
title: JS函数节流与函数防抖
date: 2019-09-22 13:38:42
tags:
 - JS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数防抖与函数节流这两个概念可能不是这么经常会遇到，但是在很多场景却会经常遇到，比如说应用于常见的很多的事件，那么究竟什么是函数节流呢，什么又是函数防抖呢，其实这也不是什么复杂的概念，这里就来好好的谈一谈。
<!-- more -->

### 概念
#### 函数节流
>所谓节流，就是指连续触发事件但是在 n 秒中只执行一次函数。

意思其实也是简单明了，就是这个函数在一定的时间内就一定只能执行一次。


#### 函数防抖
>所谓防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间。

这句话可能一时间不是特别的好理解，但是换句话说，这句话的意思就是说如果连续执行一个函数，那么这个函数只有在最后一次才会生效。

### 何时需要使用
函数节流与函数防抖通常都是应用与各种事件，因为很多事件通常会有非常高的触发频率，例如 resize 、input 、mouseMove 这类事件，通过函数节流与函数防抖就可以很好的限制其频率在我们理想的一个范围之内。

**比如函数节流的应用场景：**
+ 页面资源滚动加载

+ 高频率的重复点击

+ 防止鼠标滚轮一次滚太多

其实不止这些，任何想要限制频率来减少资源的消耗的函数都可以通过函数节流来实现。

**函数防抖的应用场景：**
+ resize获取窗口大小

+ 文本框输入验证

很多场景都会用到函数防抖来只触发一次函数。

### 实现
#### 函数节流

```js
function throttle(func, wait, options) {
  let time, context, args, result;
  let previous = 0;
  if (!options) options = {};

  let later = function() {
    previous = options.leading === false ? 0 : new Date().getTime();
    time = null;
    func.apply(context, args);
    if (!time) context = args = null;
  };

  let throttled = function() {
    let now = new Date().getTime();
    if (!previous && options.leading === false) previous = now;
    let remaining = wait - (now - previous);
    context = this;
    args = arguments;
    if (remaining <= 0 || remaining > wait) {
      if (time) {
        clearTimeout(time);
        time = null;
      }
      previous = now;
      func.apply(context, args);
      if (!time) context = args = null;
    } else if (!time && options.trailing !== false) {
      time = setTimeout(later, remaining);
    }
  };
  return throttled;
}
```
这个函数节流的功能比较全面，拥有第三个参数 ``options`` 为一个对象：
+ leading：false 表示禁用第一次执行
+ trailing: false 表示禁用停止触发的回调

可以根据情况来配置参数来实现想要的功能。

#### 函数防抖
通过 setTimeout 来实现：
```js
const _debounce = (func, wait) => {
  let timer;

  return () => {
    clearTimeout(timer);
    timer = setTimeout(func, wait);
  };
};
```
也非常的好理解，在函数执行之前如果又调用了函数则会重新开启定时器。


### 总结
这里介绍了关于函数节流与函数防抖的概念与实现，遇到类似需要的场景的时候就可以快速合理的应用起来以避免不必要的资源消耗。


### 参考
<a href="https://www.jianshu.com/p/f9f6b637fd6c">浅析函数防抖与函数节流</a>
<a href="https://blog.csdn.net/beijiyang999/article/details/79836463">JavaScript 函数节流（throttle）的实现</a>


