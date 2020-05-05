---
title: vue-js的学习（按键修饰符及自定义按键修饰符）
date: 2019-06-18 09:13:28
tags:
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在vue中可以通过键盘修饰符来控制元素在按下相应按键时的动作。vue中提供了部分内置的键盘修饰符按键码的别名，但是不是很多，那么在vue中也是可以自定义的。
<!-- more -->

### 案件修饰符的使用
在监听键盘事件时，我们经常需要检查详细的按键。Vue 允许为 v-on 在监听键盘事件时添加按键修饰符： 
```
<input v-on:keyup.enter="test()">
```

### 可以通过按键码来实现
```
<input v-on:keyup.13="test()">
```
enter键的按键码就是13，所以等同于上面那个例子。

### vue中自带的别名
* .enter
* .tab
* .delete (捕获“删除”和“退格”键)
* .esc
* .space
* .up
* .down
* .left
* .right

### 自定义案件修饰符

可以通过这样的方式来自定义按键修饰符：
```

Vue.config.keyCodes.f2 = 113;

```
然后就可以通过按下f2来触发了。


### 系统修饰符
* .ctrl
* .alt
* .shift
* .meta

通过系统修饰符可以控制系统按键与其他事件的组合：
```
        <input type="text" @keyup.13.ctrl="test()" >
```
以上代码就需要同时按回车以及control键才能触发。


### 鼠标按钮修饰符
2.2.0 新增

* .left
* .right
* .middle

像是这样：
```
        <input type="text" @click.left="test()" >
```

