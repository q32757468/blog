---
title: 理解js中的内存空间与垃圾回收
date: 2019-07-11 14:56:40
tags:
 - JS
categories:
top: 99
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;想要更加的了解js就需要对它的运作机制有更加深入的了解，这次来了解以下js的内存空间与垃圾回收机制，通过理解这两个概念对我们的开发十分有帮助。
<!-- more -->
### 内存空间
我们都知道js中的数据类型有基础数据类型与引用数据类型之分，二者的运作机制也不一样。

#### 基础数据类型与栈内存
+ 我们知道在创建执行上下文时会找到所有变量声明，并且创建VO同时消耗一定的内存空间，当到达执行阶段的时候VO成为AO，所有变量都可以访问。

+ 当变量的值被修改时就会直接在相应栈内存空间对变量的值进行修改。

+ 当复制一个变量会直接在VO中创建这个变量的值的副本，同时占用新的栈内存空间。

<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/z*R*IeVf8ofKH3UImPKBD2VQgzyGlwQ5Sz8lcpBZPgc!/b/dLsAAAAAAAAA&bo=NgEUAgAAAAADBwM!&rf=viewer_4">

### 引用数据类型与堆内存
+ 而引用类型的数据在创建执行上下文后，到达执行阶段时，变量保存的实际上是对象的引用，保存的也就是堆内存中堆内存的地址。

+ 当复制一个引用类型的值的时候也会在VO中创建一个值的副本，但是区别在于基础类型的值就是栈内存空间中的值，而引用类型的值是一个地址，所以即使复制了一份，其实两个变量引用的是同一个对象。

+ 当复制保存着对象的某个变量时，操作的是对象的引用。但在为对象添加属性时，操作的是实际的对象，所以修改复制的变量，也会影响到被复制的哪一个变量。

<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/WeIMtcs*I.Iyfd6yw*nID7ZWqUtzAUsGG2vMMsDBERo!/b/dFMBAAAAAAAA&bo=OQPVAQAAAAADF9w!&rf=viewer_4">

### 垃圾回收机制
js有自动的垃圾回收机制，所有通常我们可以不用管垃圾回收这件事，而垃圾回收一般有两个策略。

#### 标记清除
标记清除说简单点就像名字这样理解，清除被标记的变量。

+ 垃圾收集器会在运行时给内存中所有的变量都加上标记

+ 然后会去掉环境中的变量，以及环境中引用的变量的标记

+ 删除那些被标记的变量以及他们的值并且回收相应的内存空间

>到 2008年为止，IE、Firefox、Opera、Chrome和 Safari的 JavaScript实现使用的都是标记清除式的 垃圾收集策略（或类似的策略），只不过垃圾收集的时间间隔互有不同。

#### 引用计数
引用计数就是记录变量的引用次数，当变量每被使用一次，赋值给另一个变量，该变量的引用次数就加以，被赋值的变量被其他变量赋值，该变量引用次数就减一。如果这个变量引用次数为0则在垃圾收集器运作时将其回收。

>最早是Netscape使用这种方法，但是因为这种方法存在问题，所以后来被弃用了。

###垃圾回收与内存空间及其优化
+ 局部变量只在函数执行的过程中存在。而在这个过程中，会为局部变量在栈（或堆）内存上分配相应的空间，以便存储它们的值。然后在函数中使用这些变量，直至函数执行结束。此时，局部变量就没有存在的必要了，因此可以释放它们的内存以供将来使用。在这种情况下，很容易判断变量是否还有存在的要

+ 由于分配给web浏览器的内存空间通常相比桌面应用程序要更加的少，所以要尽可能都优化内存的消耗。

+ 但是全部变量就不一样，全局变量就在那，不知道你什么时候会用它，并不会被回收，所以并不推荐使用过多的全局变量，推荐使用``let``与``const``来创建块级变量，减少内存的消耗。

+ 如果使用了全局变量，最好的处理方式就是在这个变量不再使用就将其设置为``null``以优化内存消耗。

### 总结
通过梳理清楚js中有关内存相关的知识，可以有对js更加深的认识，对开发以及性能会有很大帮助。