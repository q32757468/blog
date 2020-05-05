---
title: ES6的使用
date: 2019-07-10 18:30:10
tags:
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ES6， 全称 ECMAScript 6.0 ，是 JavaScript 的下一个版本标准，2015.06 发版。ES6 主要是为了解决 ES5 的先天不足，比如 JavaScript 里并没有类的概念，但是目前浏览器的 JavaScript 是 ES5 版本，大多数高版本的浏览器也支持 ES6，不过只实现了 ES6 的部分特性和功能。ES6的发布到现在已经有很长一段时间了，各个浏览器的新版本也都支持ES6的大部分语法，使用ES6能够极大的提高我们开发的效率以及使提高代码的可读性。
<!-- more -->

使用ES6来书写代码可以很大程度上的提高代码的书写效率，并且使用也相对简便，新的ES6语法涉及的还是挺多的，这里就介绍使用一些比较常见并且实用的ES6语法。

### 声明关键字 let 与 const

在ES5中我们都是只是用 var 关键字来声明变量，但是 var 存在很多的缺陷，比如定义很多全局变量会导致变量名称冲突，全局变量不好回收，导致占用内存过多需要手动释放等等，使用 let 与 const 就可以有效的避免这些问题。

先来看看他们之间的区别：
{% raw %}
  <table style="text-align: center">
    <tr>
      <td> </td>
      <td>var</td>
      <td>let</td>
      <td>const</td>
    </tr>
    <tr>
      <td>作用域</td>
      <td>全局、函数</td>
      <td>代码块</td>
      <td>代码块</td>
    </tr>
    <tr>
      <td>重新赋值</td>
      <td>能</td>
      <td>能</td>
      <td>不能</td>
    </tr>
    <tr>
      <td>变量提升</td>
      <td>能</td>
      <td>不能</td>
      <td>不能</td>
    </tr>
    <tr>
      <td>重复声明</td>
      <td>能</td>
      <td>不能</td>
      <td>不能</td>
    </tr>
  </table>
{% endraw %}

  + 其实通过这张表格就能很清楚的知道他们之间的区别了

  + 这里的代码块指的是大括号内，也就是`` { } ``，在大括号中定义的变量在大括号外部就无法访问了。
    ```
    {
      let a = 30;
      const b = 40;
    }
    console.log(a);//报错未定义
    console.log(b);//报错未定义
    ```

  + 不支持变量提升
    ```
    {
      console.log(a, b);//报错
      let a = 100;
      const b = 10;
    }
    ```
  + const无法重新赋值
    ```
    {
      const b = 10;
      b = 100;    
      //报错
    }
    ```
  + 暂时性死区
    ```
    var a = 100;
    {
      console.log(a);
      let a;
      //报错
    }
    ```
    ES6 明确规定，代码块内如果存在 let 或者 const，代码块会对这些命令声明的变量从块的开始就形成一个封闭作用域。代码块内，在声明变量 变量 之前使用它会报错。

推荐不要使用 var 关键字了，全部用 let 与 const 代替。

### 箭头函数
ES6为我们提供了箭头函数。
  + 使用箭头函数可以使代码的可读性更强
  
  + 并且使用箭头函数可以更好的明确``this``的指向，就不用特别麻烦的去考虑this的指向了
    
    - 在不使用箭头函数的情况下函数中的this总是指向调用者，就是说谁最后调用了该函数，那么this就指向谁，此时的this指向是动态的。
      ```
    const name = 'csz2';
    {
      const obj = {
        name: 'csz',
        foo() {
          console.log(`${this.name}fcnb`);  //cszfcnb
          console.log(this);                //{name: "csz", foo: ƒ}
        }
      }
      obj.foo();   //cszfcnb  //{name: "csz", foo: ƒ}
      const bar = obj.foo;
      bar();    //csz2fcnb  //Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
    }
    ```
    - 但是动态的this函数指向在使用回调函数的时候经常会得不到我们想要的结果
    ```
    var name = 'csz3';
    {
      const obj = {
        name: 'csz',
        chara: ['fcyq'],
        foo() {
          this.chara.map(function (chara) {
            console.log(`${this.name}${chara}`);
          })
        }
      }
      obj.foo();    //结果是  csz3fcyq  此时的this指向了 Window ,很显然这不是我们想要的。
    }
    ```

    - 在使用了箭头函数以后 this 的指向就会变成静态的，即在创建函数时 this 指向谁那么调用时this 也指向谁
    ```
    var name = 'csz3';
    {
      const obj = {
        name: 'csz',
        chara: ['fcyq'],
        foo() {
          this.chara.map(chara => {
            console.log(`${this.name}${chara}`);
          })
        }
      }
      obj.foo();    //结果是  cszfcyq  这样就解决了上面出现的问题
    }
    ```

    - 所以合理的使用箭头函数可以使开发更加的简便

    - 由于使用了箭头函数之后this的指向是静态的，所以在有些情况下也是不适合使用箭头函数的
      例如在创建构造函数时，在对象中创建方法时，各种事件的回调等等，这些情况都不适合用回调函数，否则会出现很多意想不到的错误。


### 解构赋值
使用结构赋值可以很轻松的给数组或者是赋值。

#### 数组解构
+ 基本：
```
    {
      const arr = [1, 2, 3, 4, 5, 6];
      const [a, b, c] = arr;
      console.log(a, b, c);//结果1 2 3
    }
```

+ 可以使用默认值：
```
    {
      const arr = [1, 2];
      const [a, b, c = 30] = arr;
      console.log(a, b, c);//结果1 2 30
    }
```
但结果为 undefined 就会使用默认值。

+ 可以使用剩余运算符
```
    {
      const arr = [1, 2, 3, 4, 5, 6];
      const [a, b, ...c] = arr;
      console.log(a, b, c);//结果1 2 (4) [3, 4, 5, 6]
    }
```
默认运算符的结果将保存为数组。

#### 对象解构
对象结构与数组解构类似，但是也有区别：

+ 基本：
```
    {
      const obj = {
        a: 10,
        b: 20,
        c: 30
      };
      const { a, b, c } = obj;
      console.log(a, b, c);//10 20 30
    }
```

+ 重命名
由于有时变量名并不一定和对象中的属性名相同，所以可以重命名
```
    {
      const obj = {
        a: 10,
        b: 20,
        c: 30
      };
      const { a: A, b: B, c: C } = obj;
      console.log(A, B, C);//10 20 30
    }
```

>大部分都与数组解构类似所以不做演示了。

### 剩余运算符
+ 使用剩余运算符可以一次性的拿到所有的剩余的参数或者是对象、数组中的元素

+ 可以在解构赋值时使用
参考本篇博客解构赋值。

+ 可以用获取函数参数：
```
    {
      function foo(...arg) {
        console.log(arg);
      }
      foo(1, 2, 3, 4, 5, 6);//结果(6) [1, 2, 3, 4, 5, 6]
    }
```
我们可以看出在获取参数方面和 ``arguments`` 有一些相似，都不关心传了多少个参数，想用某个参数直接从数组中调用就可以了
但是他们之间还是有很明显的区别的：
    - 剩余运算符需要进行设置，需要传递一个剩余运算符名称，通过这个名称来获取参数。而``arguments``不用设置，直接使用arguments来调用参数就可以了。

    - 剩余运算符是一个真正的数组，可以调用数组的方法。而``arguments``是一个伪数组，不能调用数组的相关方法。
    

### 模板字符串
+ ES6新增了一种字符串的包裹方式，使用``  `  ``反引号进行包裹。这就是模板字符串。

+ 模板字符串中可以使用变量。通过使用``${}``包裹变量就可以变量在能够在模板字符串解析,``${}``内可以是变量也可以是任意表达式。
```
    {
      const a = 10;
      const b = 20;
      const str = `a+b=${a + b}`;
      console.log(str);//结果a+b=30
    }

```
可以看出使用模板是十分的方便，再也不用麻烦的拼接以及检查。

+ 模板字符串顾名思义可以用来定义模板：

```
    {
      const template = `
      <div class="container">
        <div class="row">
          <h1>这是标题</h1>
          <p><span>😊</span> 这是一段文字描述</p>
        </div>
      </div>`;
      console.log(template);
      document.querySelector('body').innerHTML = template;
    }
```
如果在ES5中我们要书写一个模板并且要保持一定的格式是非常麻烦的一件事情，因为涉及到众多双引号有时候还会有单引号，如今在ES6中我们直接使用模板字符串就可以很轻松的定义一个可读性非常强的模板。十分的方便。

### 扩展运算符
``扩展运算符`` 的作用与剩余运算符的作用相反， ``剩余运算符``  是可以将剩余部分合并成为一个数组，而  扩展运算符  则是可以将可遍历对象进行拆分。

+ 使用非常简单，在需要拆分的可遍历对象前面加``...``即可：
 ```
    {
      const str = 'asdfghjkl';
      const obj = { a: 10, b: 20, c: 30 };
      const arr = [1, 2, 3, 4, 5, 6];
      console.log(...str);      //a s d f g h j k l
      console.log({ ...obj });  //{a: 10, b: 20, c: 30}
      console.log([...arr]);  //6) [1, 2, 3, 4, 5, 6]
    }
 ```
+ 用来拼接
```
    {
      const arr = [1, 2, 3, 4, 5, 6];
      const arr2 = [...arr, ...arr];
      console.log([...arr2]);   //(12) [1, 2, 3, 4, 5, 6, 1, 2, 3, 4, 5, 6]
    }
```
通过扩展运算符就可以十分轻松的对可遍历对象进行拼接或者是对数组或者是对象的深拷贝。

+ 作为参数传递给函数
```
    {
      function sum(...num) {
        return num.reduce((pre, cur) => pre + cur);
      }
      const nums = [1, 2, 3, 4, 5, 6];
      console.log(sum(...nums));
    }
```

就像这个求和函数一样，我们要给数组求和，不用关心数组里面有多少元素，直接将数组扩展为函数的参数就可以进行求和了，十分的简单便捷。

### 新的迭代器
js中原来存在的这几种迭代方式都存在一些不方便的地方：
  + for 循环在很多场景下不够简便，并且代码可读性较差
  + forEach 循环不能够使用 ``break``和``continue``
  + for in 循环遍历的是遍历对象上的所有可枚举属性，甚至包括原型属性：
    ```
    {
      Array.prototype.test2 = '456';
      const arr = [1, 2, 3, 4, 5, 6, 7];
      arr.test = '123';
      for (index in arr) {
        console.log(arr[index]);
      }
    }
    ```
    <img src="http://m.qpic.cn/psb?/V131x4904WMIoW/96r62kRzScMB5MKAr1Y5TactkvsfCCafy7k7fBBJkkY!/b/dFMBAAAAAAAA&bo=sQP3AAAAAAADB2c!&rf=viewer_4">

    可以看出使用for in 循环不仅会把属性遍历出来，连数组原型上的属性都会遍历出来。

所以 for of 循环就产生了：
+ 基本：
```
    {
      const arr = [1, 2, 3, 4, 5, 6];
      for (num of arr) {
        console.log(num); //1 2 3 4 5 6
      }
    }
```
for of 循环可以很轻松的遍历数组中的元素。

+ 通过``entries()``方法同时遍历索引和值：
```
    {
      const arr = [1, 2, 3, 4, 5, 6, 7];
      for ([index, item] of arr.entries()) {
        console.log(`index:${index},item:${item}`);
      }
    }
```

+ 可以使用 break 和 continue
```
    {
      const arr = [1, 2, 3, 4, 5, 6, 7];
      for (num of arr) {
        console.log(num);
        if (num == 3) {
          break;
        }
      }
    }
```
结果： 1 2 3

>通过以上可以看出在原生js中，迭代器中for of 循环是最强大的。


### 总结
ES6为我们提供了一系列的新的功能，可以看出这些功能十分的强大，加以利用可以很大程度的提高我们的开发效率。
