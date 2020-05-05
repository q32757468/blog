---
title: vue.js的学习（七）过滤器filter的使用
date: 2019-06-17 20:46:29
tags:
 - JS
 - VUE
categories:
 - 学习笔记
 - VUE学习笔记
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;filter过滤器可以用来格式化数据，只能用在插值表达式或者v-model之类的组件上面。分为全局过滤器和私有过滤器，在定义的方法上稍有区别。
<!-- more -->

### 定义全局过滤器 
Vue.js 允许你自定义过滤器，可被用于一些常见的文本格式化。过滤器可以用在两个地方：双花括号插值和 v-bind 表达式 (后者从 2.1.0+ 开始支持)。过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符号指示：
```html
    <td>{{item.time | dateformat}}</td>
```

```js
Vue.filter('dateformat',function(date){
            var dt=new Date(date);
            var y=dt.getFullYear();
            var m=dt.getMonth()+1;
            var d=dt.getDate();
            var h=dt.getHours();
            var min=dt.getMinutes();
            var s=dt.getSeconds();
            return `${y}-${m}-${d} ${h}:${min}:${s}`;
        });

```

过滤器可以串联：
```html
{{ message | filterA | filterB }}
```
在这个例子中，filterA 被定义为接收单个参数的过滤器函数，表达式 message 的值将作为参数传入到函数中。然后继续调用同样被定义为接收单个参数的过滤器函数 filterB，将 filterA 的结果传递到 filterB 中。

过滤器是 JavaScript 函数，因此可以接收参数：
```
<td>{{item.time | dateformat('yyyy-mm-dd')}}</td>
```
```js
 Vue.filter('dateformat',function(date,agr=''){//最好给参数设置一个默认值，防止出现bug
            var dt=new Date(date);
            var y=dt.getFullYear();
            var m=dt.getMonth()+1;
            var d=dt.getDate();
            var h=dt.getHours();
            var min=dt.getMinutes();
            var s=dt.getSeconds();
            if(agr=='yyyy-mm-dd'){
                return `${y}-${m}-${d}`; 
            }
            else
            return `${y}-${m}-${d} ${h}:${min}:${s}`;
        });
```


### 定义私有过滤器
```js
            filters:{
                dateformat(date,agr=''){
                    var y=dt.getFullYear();
                    var m=(dt.getMonth()+1).toString().padStart(2,'0');
                    var d=dt.getDate().toString().padStart(2,'0');
                    var h=dt.getHours().toString().padStart(2,'0');
                    var min=dt.getMinutes().toString().padStart(2,'0');
                    var s=dt.getSeconds().toString().padStart(2,'0');
                    if(agr=='yyyy-mm-dd'){
                        return `${y}-${m}-${d}`; 
                    }
                    else
                    return `${y}-${m}-${d} ${h}:${min}:${s}`;                    
                }
            },
```
通过一个对象来设置，整体与全局设置的方法差不多。


### 学习到的一些知识
* 可以通过`` `${y}-${m}-${d} ${h}:${min}:${s}` ``像这样的方式来格式化数据，`` ${} ``为占位符。
+ 可以通过padStart(len, str)与padEnd(len, str)这两个方法来补全字符串，但是要注意只能补全字符串，所以通常需要先通过tostring()方法转化为字符串
    - 根据给定长度自动在字符串的前面补充想补充的字符串（只返回修改后的字符串，不修改原字符串）
    - len 给定的长度，转换后
    - str 想补充的字符串
类似这样：
```
    var s=dt.getSeconds().toString().padStart(2,'0');

```

### 总结
通过定义filter可以很方便的来格式化数据，这里还有一个小细节，就是带s的就是私有的，不带s的就是全局的。