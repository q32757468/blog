---
title: jQuery原理解析
date: 2019-09-15 16:44:24
tags:
 - JS
 - jQuery
categories:

---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在刚开始学习jQuery的时候就很配置能写出这样的库的人，虽然我能想到jQuery是通过内部封装了大量的方法来实现函数的扩展的，当时并不了解jQuery究竟是怎样将一个dom对象处理成一个jQuery对象的，又是如何处理其他对象的，直到我阅读了jQuery的源码还有结合别人对jQuery源码的理解我才理解了jQuery的源码的机制，也不得不感叹很多地方是如此的巧妙。所以这篇文章主要讲一下jQuery的核心函数以及一些机制。虽然jQuery已经有些过时了，当时我觉得学习了解这款优秀的项目也是提升自己的一大途径。
<!-- more -->
### jQuery的基本结构
理解jQuery源码，可以从jQuery的结构开始，jQuery是采用构造函数模式开发的，jQuery本身就是一个构造函数，是一个类而jQuery中的大部分的方法都是再这个类的原型上的，所以只有jQuery的实例才能使用这些方法或者是属性，而这些我们也可以通过它的源码看出：
```js
        (function( window, undefined ) {
            var jQuery = function( ) {
                return new jQuery.prototype.init( );
            }
            jQuery.prototype = {
                constructor: jQuery
            }
            jQuery.prototype.init.prototype = jQuery.prototype;
            window.jQuery = window.$ = jQuery;
        })( window );
```
这可以说就是jQuery最基本的结构了

首先是最外层是用了匿名的立即执行函数来包裹以防止污染全局，至于为什么要给这个函数传递一个 window 作为参数这是因为方便代码的压缩，形参中的 window 在代码压缩之后可能就不是 window 了，为了能找到正确的window所以可以这样设置。

接下来就是名为 jQuery 的构造函数了，这也是整个 jQuery 中最为核心的一部分了，这个构造函数中包含了jQuery中的一些很重要的方法，这其中就包括**创建一个jQuery对象**时所用到的 ``init()`` 方法,你可能会奇怪为什么创建jQuery对象的会是 ``init()`` 方法而不是我们平时所用的 jQuery这个构造函数呢，其实我们通过源码也可以看出，``init()`` 这个方法是 jQuery 构造函数中的一个方法，当我们要通过 jQuery构造函数创建一个 jQuery对象的时候向核心函数传递了需要创建的对象，但是实际上返回的是通过 ``init()`` 这个构造函数所创建的实例对象，再修改 init 的原型，虽然这个实例不是jQuery构造函数的实例但却有着一样的原型，而这个实例就是我们俗说的jQuery对象。这样就实现了通过jQuery构造函数中的方法创建了一个jQuery的对象。

最后再将 jQuery 和 $ 注册为全局，这样就实现了jQuery最基本的一个结构，可以在全局中使用jQuery了，我觉得这种方式是很巧妙的， 不看源码的话我是怎么都想不明白究竟是怎么回事，不过这也是可以学习的地方，理解了jQuery的源码是怎么回事，就可以自己尝试着开发一个类似的函数库了。


### jQuery构造函数接收不同的数据
经过前面代码的了解我们现在知道其实jQuery构造函数接收数据其实真正进行处理的是它里面的 init 方法，因为我们使用过jQuery所以我们知道接收不同的参数，所返回的数据也是不一样的，比如说接收一个选择器返回的就是一个jQuery对象，如果接收的是一个空字符串之类的话返回的也就是一个空的jQuery对象，我们可以来看一下究竟是怎么样返回一个jQuery对象的，可以先看看jQuery的源码：
```js
	init: function( selector, context, rootjQuery ) {
		var match, elem;

		// HANDLE: $(""), $(null), $(undefined), $(false)
		if ( !selector ) {
			return this;
		}

		// Handle HTML strings
		if ( typeof selector === "string" ) {
			if ( selector.charAt(0) === "<" && selector.charAt( selector.length - 1 ) === ">" && selector.length >= 3 ) {
				// Assume that strings that start and end with <> are HTML and skip the regex check
				match = [ null, selector, null ];

			} else {
				match = rquickExpr.exec( selector );
			}
        }
```
这是 init 方法中的一小部分的源码，通过这里我们可以看出这是通过if语句判断接收的是什么类型的数据从而返回符合期望的实例对象，知道了这一点我们就很好理解jQuery是如何创建一个jQuery对象的了。

### jQuery对象的属性与方法
通过前面我们已经可以了解到其实jQuery对象就是通过jQuery构造函数中的 init 方法创建的一个实例，所以就很好理解了，jQuery对象所拥有的属性与方法全都是来自于jQuery构造函数的原型，通过原型机制 init 实例最终会去jQuery构造函数的原型中去找，所以就能找到所需要的属性或是方法，可以通过一张图来更加容易的理解：
<img	src="http://m.qpic.cn/psb?/V131x4904WMIoW/qNnXhAg3tLkK3lSCVzuas5G8eUg3Vci0gnV6FGwR1W0!/b/dL8AAAAAAAAA&bo=xgJXAgAAAAADB7M!&rf=viewer_4">

jQuery对象要调用某个jQuery中的方法或者属性的时候就会这样去找：
init实例（jQuery对象）=> init构造函数 => jQuery.prototype


### 总结
jQuery作为一款比较常用的库，理解学习其原理还是非常有必要的，对于提升自己也是非常有帮助的，可以多看看优秀的项目的源码来提升自己，学习别人的思路。


