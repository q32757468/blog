---
title: JS中的基本包装类型
date: 2019-07-16 11:38:20
tags:
 - JS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们都知道js中的数据有两大类型，基本数据类型和引用类型，但是我们也知道从逻辑上来说其实基本类型的数据其实并不是对象，也就没有属性和方法，但实际上我们在使用的过程中这些基本类型的数据也是有属性和方法的，其实这主要是依赖于基本包装类型。
<!-- more -->
### 理解基本包装类型
+ 那么为什么我们使用的基本类型数据会有方法和属性呢，其实主要是由于基本包装类型的原因，当我们在对基本类型的数据调用一个属性或者方法时其实后台还会有一系列的操作来进行转换，从而能够正确的获取结果，我们可以像下面这样理解：
```
{
  const str='这是一个字符串';
  const len=str.length;
}
```
+ 通过这样的操作我们可以length这个属性来获取到字符串的长度，并且将这个长度赋值给 len 这个变量。、

+但实际上它在后台的操作可以这样进行理解：
```
{
const str_ = new String('这是一个字符串');  //创建基本包装类型的对象，并且自动获取到字符串的值
const len=str_.length;    //调用基本包装类型对象的属性来获取长度
str_=null;              //调用结束后销毁对象
}
```

通过上面的例子我们就可以很简单的理解基本包装类型了，基本包装类型就像是一个中转站一样，虽然基本类型的数据不是对象，但是通过后台的操作创建一个基本包装类型对象，所以我们调用基本类型的属性或者是方法实际上调用的是基本包装类型的属性或者方法，当调用完毕后再进行销毁，最终我们看到的结果就像是基本数据类型有属性或者是方法一样。

### 不能给基本类型数据添加属性或者方法
我们可以看出虽然基本类型数据表面上看起来可以调用数据和方法，但是实际上我们也还是不能够给他们添加方法或者是属性，因为他们根本上还是基本类型数据，并且基本包装类型对象在执行完毕后会立即销毁：
```
    {
      var str2 = '字符串';
      str2.name = 'csz';
      console.log(str2.name);//报错，不能在字符串上创建
    }
```

### 可以创建基本包装类型对象
我们可以通过显示调用``Boolean、Number、String``来创建基本包装类型对象。
```
    {
      const str = new String('这是一个通过构造函数创建的字符串');
      const num = new Number(1234);
      const res = new Boolean(true);
      const str2 = new Object('这是一个通过Object构造函数创建的字符串');
      console.log(typeof str);//object
      console.log(str instanceof String);//true
      console.log(typeof num);//object
      console.log(num instanceof Number);//true
      console.log(typeof str2);//object
      console.log(str2 instanceof String);//true
    }
```
可以看出是可以通过显示的创建基本包装类型对象的，甚至可以直接通过``Objct``构造函数自动识别来创建，但是并不推荐通过这种方式来创建基本类型数据，因为这样创建的数据通过``typeof``判断的是一个对象，而不是基本数据类型。

特别是在创建Boolean类型数据时尤为要注意：
```
    {
      const res = new Boolean(false);
      console.log(res && true);//true 
      const res2 = false;
      console.log(res2 && true);//false
    }
```
得到这个结果也并不奇怪，因为通过基本包装类型创建的数据实际上是一个对象，当进行比较运算时，对象都是一律按``true``处理的，所以这里并不能得到我们想要的结果。

>所以建议永远不要通过实例化构造函数的形式来创建Boolean类型的数据。

### 总结
基本包装类型很好理解，了解基本包装类型对于对于理解js运作机制也很有帮助，还有就是一些要注意的地方，比如不要不要通过实例化构造函数的形式来创建Boolean类型的数据。