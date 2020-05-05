---
title: JS原型与原型链以及继承
date: 2019-07-19 18:46:26
tags:
 - JS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在js对象中有一个非常重要的概念，就是原型，通过原型模式创建对象就可以共享特定的属性以及方法，合理的使用就可以减少代码量以及减少资源的消耗。这里提到原型就不得不提一下原型搜索机制：当以读取模式访问一个实例属性时，首先会在实例中搜索该属性。如果没有找到该属性，则会继续搜索实例的原型，这是在没有原型链的情况下，在通过原型链实现继承的情况下，搜索过程就得以沿着原型链继续向上。实际上原型链就是扩展了原型的搜索范围。而原型链的产生主要是通过继承来实现，这就是原型与原型链及继承这三者之间的关系。
<!-- more -->
### 原型与原型对象
在理解原型链与继承之前需要理解原型与原型对象的概念：

+ 我们都知道，我们创建的每一个函数都拥有一个``prototype``属性，这个属性的属性值是一个指针，指向一个对象，而这个对象就是原型对象，其中保存了我们定义的所有在原型上的属性或者方法，那么只要是在原型对象当中，那么这个函数的所有实例就可以访问到其中的属性或者方法，所以所这些属性和方法是共享的。

+ 在原型对象当中，有一个属性叫做``constructor``，这个属性的属性值是一个指针，指向了当初添加了原型的那个函数。

+ 当调用了函数创建了一个实例对象的时候，这个实例内部又还会有一个属性，这个属性是一个指针叫做``[[prototype]]``，而这个指针就指向了原型对象,对于这个属性的访问没有定义相关的标准，但是在Firefox、chrome、safari中每个实例对象都可以看到一个__proto__属性，其它浏览器则是完全不可见。

+ 构造函数、原型、原型对象这三者之间的关系可以这样理解（假设有一个Person构造函数并且有相应的原型对象）：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/l1zNaitxZQ3jFWO5QacsM47xsgfZLDvec*pb00eWCOM!/b/dMMAAAAAAAAA&bo=IQWHAQAAAAADB4A!&rf=viewer_4">
通过这张图就可以很直观的看出三者之间的关系了。

>值得注意的一点是由于原型中查找相应的值是一次搜索过程，所以一旦修改了原型对象，所发生的改变就会立刻反映出来，实例或者构造函数中原型的值也会立刻跟着变化，无论创建的先后。

>正如上面所提到可以修改原型对象，一旦重写了原型对象就会导致constructor的指针发生变化，如果需要则要手动设置constructor.

### 原型链
+ ECMAScript中描述了**原型链**的概念，并将原型链作为实现继承的主要方法。

+ 前面我们已经简单了解过原型的相关知识了，那么原型链其实顾名思义就像一条链条一样，这条链条上全都是各种不同的原型，而我们由前面就可以知道属性的搜索机制是先在当前实例中找，找不到再到原型中找，通过原型链连接许多不同的原型就可以扩大属性的搜索范围，这就是原型链的作用。

+ 这是实现原型链的一种基本模式：
```js
    function Test1() {
      this.name1 = 'csz1';
      Test1.prototype.name2 = 'csz2';
    }
    function Test2() {
      this.name3 = 'csz3';
      Test2.prototype.name4 = 'csz4';
    }
    Test2.prototype = new Test1();
    const obj = new Test2();
    console.log(obj.name1);//通过原型链访问到Test1 csz1
    console.log(obj.name2);//通过原型链访问到Test2 csz2
    console.log(obj.name3);//在实例中找到          csz3
    console.log(obj.name4);//在原型中找到          csz4
```
可以看出通过手动将Test1构造函数的实例绑定到Test2的原型上形成一条原型链，Test2的实例就可以通过原型链访问到Test1实例中的属性以及原型中的属性。

这条原型链可以简单的像这样表示：
``obj[[prototype]] => Test2.prototype => new Test1() => Test1.prototype => Object.prototype``

>需要注意的一点是在设置原型的时候最好不要通过对象字面量来创建``原型方法``，这会导致重写原型链，从而致使原型链被切断。


### 继承
 许多OO语言都支持两种继承模式，接口继承与实现继承，ECMAScript则只支持实现继承，并且将原型链作为实现继承的主要方法。

 通过上面对原型链的理解，我们也知道如何通过原型链来实现继承（主要就是通过搜索机制）。

 主要有六种继承的方式：

#### 一、通过原型链来实现继承

+ 通过上面原型链的例子我们知道可以通过将一个构造函数的实例设置给另一个构造函数的原型通过原型链来实现继承，并且可以看出原型链的作用十分强大。

+ 但是直接通过原型链来实现继承也存在一些问题，我们可以看看下面这个例子：
```js
    function Test1() {
      this.name1 = 'csz1';
      this.arr = [1, 2, 3, 4, 5, 6];
      Test1.prototype.name2 = 'csz2';
      Test1.prototype.fn = function () {
        return 'test1';
      };
    }
    function Test2() {
      this.name3 = 'csz3';
      Test2.prototype.name4 = 'csz4';
    }
    //将Test的原型设置为Test1的实例，这条语句必须写在Test2外面
    Test2.prototype = new Test1();
    Test2.prototype.constructor = Test2;
    const obj1 = new Test2();
    const obj2 = new Test2();
    obj1.arr.push(999);
    console.log(obj1.arr);  //[1, 2, 3, 4, 5, 6, 999]
    console.log(obj2.arr);  //[1, 2, 3, 4, 5, 6, 999]
```
可以看出当我们通过 obj1 这个实例修改了数组 arr 中的值，结果导致 obj2 中数组 arr 中的值也一起跟着修改了，那么为什么会出现这样的结果呢，其实我们不难发现当我们在执行这条语句的时候``Test2.prototype = new Test1();``是直接将一个实例设置给了 Test2的原型，这就会导致原本在Test1实例属性中的数组到了Test2中就变成了原型中的数组，那么就相当于在Test2的原型中设置了一个引用类型的属性一样，我们都知道原型对象中的属性相当于是共享的，所以结果可想而知。很明显得不到我们想要的结果。这是其中的一个问题。

+ 还有一个问题则是在创建子类型实例时无法很完美的给超类型传递参数（虽然可以传递，但会影响到所有的实例）。所以实践当中不推荐使用这种方式来实现继承。

#### 二、借用构造函数
为了解决上面出现的这些问题，开发人员开始使用一种叫做**借用构造函数（constructor stealing）**的技术，使用起来也很简单，在子类型构造函数中调用超类型构造函数就可以实现。

+ 可以传递参数：
```js
    function Test1(money) {
      this.name1 = 'csz1';
      this.money = money;
      this.arr = [1, 2, 3, 4, 5, 6];
      Test1.prototype.name2 = 'csz2';
      Test1.prototype.fn = function () {
        return 'test1';
      };
    }
    function Test2() {
      //借用了Test构造函数，并且可以通过call或者aplly方法来传递参数并改变this指向
      Test1.call(this, 1000);
      this.name3 = 'csz3';
      Test2.prototype.name4 = 'csz4';
    }
    const obj2 = new Test2();
    console.log(obj2.money);  //1000
```
通过这个例子可以看出通过借用构造函数可以实现参数的正常传递，获取到相对应的值。

>为了确保子类型的属性不被借用来的构造函数重写，应该确保将子类型的属性写在借用来的超类型构造函数之后。

+ 子类型实例间不会相互影响：
```js
    function Test1(money) {
      this.name1 = 'csz1';
      this.money = money;
      this.arr = [1, 2, 3, 4, 5, 6];
      Test1.prototype.name2 = 'csz2';
      Test1.prototype.fn = function () {
        return 'test1';
      };
    }
    function Test2() {
      this.name3 = 'csz3';
      Test1.call(this, 1000);
      Test2.prototype.name4 = 'csz4';
    }
    const obj1 = new Test2();
    const obj2 = new Test2();
    obj1.arr.push(999);
    console.log(obj1.arr);//1, 2, 3, 4, 5, 6, 999]
    console.log(obj2.arr);//[1, 2, 3, 4, 5, 6]
```
可以看出由于借用来的属性全在构造函数当中，不在原型当中，所以不会出现引用类型的数据相互影响的情况。

##### 借用构造函数存在的问题
由于借用构造函数借来的属性以及方法全都是在构造函数的实例当中，所以单纯的这种方式就无法访问到超类型的原型上的属性和方法:
```js
    function Test1(money) {
      this.name1 = 'csz1';
      this.money = money;
      this.arr = [1, 2, 3, 4, 5, 6];
      Test1.prototype.name2 = 'csz2';
      Test1.prototype.fn = function () {
        return 'test1';
      };
    }
    function Test2() {
      this.name3 = 'csz3';
      Test1.call(this, 1000);
      Test2.prototype.name4 = 'csz4';
    }
    const obj1 = new Test2();
    const obj2 = new Test2();
    obj1.arr.push(999);
    console.log(obj1.arr);//1, 2, 3, 4, 5, 6, 999]
    console.log(obj2.arr);//[1, 2, 3, 4, 5, 6]
    console.log(obj2.name2);//undefined
```
可以看出访问存在于超类型构造函数原型中的属性是访问不到的，所以单纯的借用构造函数也是不推荐使用的。

#### 三、组合继承
为了解决前面提到的两种方法的缺点，就出现了组合继承，组合继承简单来说就是将通过**原型链**来继承和通过**借用构造函数**来继承这两方法进行组合，互相取长补短，发挥二者的优势，以达到一个较好的继承效果。其实现思路是通过**原型链**的方式来继承超类型原型上的属性和方法，通过**借用构造函数**来继承超类型中构造函数上的属性和方法。

来看看具体是怎么实现的：
```js
    function Test1(money) {
      this.name1 = 'csz1';
      this.money = money;
      this.arr = [1, 2, 3, 4, 5, 6];
      Test1.prototype.name2 = 'csz2';
      Test1.prototype.fn = function () {
        return '这是test1';
      };
    }
    function Test2() {
      // 通过借用构造函数来继承实例中的属性和方法
      Test1.call(this, 999);
      this.name3 = 'csz3';
    }
    //通过原型链来继承超类型构的原型
    Test2.prototype = new Test1();
    Test2.prototype.constructor = Test2;
    Test2.prototype.name4 = 'csz4';
    const obj1 = new Test2();
    const obj2 = new Test2();
    console.log(obj1.name2);//csz2
    console.log(obj1.money);//999
    obj1.arr.push(666);
    console.log(obj1.arr);// [1, 2, 3, 4, 5, 6, 666]
    console.log(obj2.arr);// [1, 2, 3, 4, 5, 6]
```
从结果可以看出使用**组合继承**成功的集合了原型链继承和借用构造函数继承的优点，同时弥补了二者的缺点，是一种比较实用的继承方式，推荐使用。

>组合继承也并不是完全没有缺点，也存在一些小问题，像是要调用两次超类型构造函数，存在资源的浪费，但是问题不大，后面会介绍一种更加完美的继承方式，到时再详细介绍。

#### 四、原型式继承
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;原型式继承由道格拉斯·克罗克福德在2006年写了一篇文章，题为Prototypal Inheritance in JavaScript （JavaScript中的原型式继承）中介绍，他介绍了一种实现继承的方法，这种方法并没有使用严格意义上的构造函数。他的想法是借助原型可以基于已有的对象创建新对象，同时还不必因此创建自定义类型。为了实现这个目的，他给出了如下函数：
```js
function object(o) { 
  function F(){} 
  F.prototype = o; 
  return new F(); 
} 
```
+ 通过这个函数就可以看出其实这个函数的作用就是将一个已有的对象转化为一个中间构造函数的原型对象，最后通过这个函数来返回新的实例，而这个新的实例则是这个在函数中的构造函数的实例，所以新的实例就会将我们传递给函数的这个对象当作原型对象。

+ 从本质上来说，object()函数对传入其的对象进行了一次浅复制。

+ 简单一点理解就是通过这个函数可以返回一个新的对象，返回的所有新对象共享传递给函数的对象作为原型对象。与原型链模式类似，所以在在使用引用类型的数据的时候需要格外注意。

我们可以通过一个例子来理解：
```javascript
    function object(o) {
      function F() { }
      F.prototype = o;
      return new F();
    }
    obj1 = {
      name: 'csz1',
      age: 56,
      arr: [1, 2, 3, 4, 5, 6, 7]
    }
    const obj2 = object(obj1);
    const obj3 = object(obj1);
    console.log(obj2.name);//csz1
    //引用类型的数据会相互影响
    obj2.arr.push(666);
    console.log(obj3.arr);//[1, 2, 3, 4, 5, 6, 7, 666]
    console.log(obj2.arr);//[1, 2, 3, 4, 5, 6, 7, 666]
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;克罗克福德主张的这种原型式继承需要有一个对象作为基础，如果有一个现成的对象，需要根据这个对象为原型创建一个新的对象再稍作修改的话，使用这种方式就比较快速便捷。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ECMAScript5中新增了一个``Object.creat()``方法来规范原型式继承，这个方法接收两个参数，第一个参数是一个需要作为基础的对象，第二个参数是为新对象定义额外属性的对象。

+ 当只传入一个参数时效果与前面的克罗克福德的object函数一样。

+ 第二个参数的格式与可以设置新对象的额外属性，新的属性会覆盖掉作为第一个参数的那个对象的同名属性，使用格式Object.defineproperties()方法一致：
```js
    obj1 = {
      name: 'csz1',
      age: 56,
      arr: [1, 2, 3, 4, 5, 6, 7]
    }
    const obj2 = Object.create(obj1, {
      //要使用这种格式
      name: {
        value: 'csz2'
      }
    })
    console.log(obj2.name);//csz2
```
在需要使用到原型式继承时可以直接使用``Object.creat()``会更加的便捷。

#### 五、寄生式继承
寄生式（parasitic）继承是与原型式继承紧密相关的一种思路，并且同样也是由克罗克福德推而广之的。让我们来看看它是如何工作的：
```js
    obj1 = {
      name: 'csz1',
      age: 56,
      arr: [1, 2, 3, 4, 5, 6, 7]
    }
    function createAnother(original) {
      var clone = Object.create(original);
      clone.sayHi = function () {
        console.log("hi");
      };
      return clone;
    }
    const obj2 = createAnother(obj1);
    console.log(obj2.sayHi());//hi
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其实可以看出寄生式继承是原型式继承的一种扩展，相当于是把**原型式继承的函数**以及**新对象需要扩充的属性**同时封装在一个函数当中。可以在需要使用原型式继承并且需要在内部扩充属性时使用。

>注意在使用寄生式继承来为对象添加方法时不能够做到函数复用，这一点类似创建对象时的造函数模式。

#### 六、寄生组合式继承
前面提到了比较完美的**组合继承**也存在着缺陷，两次调用超类型构造函数，并且存在着资源浪费的情况（因为原型是超类型的实例所以会存在着有属性与子类型实例重名的情况，造成不必要的资源浪费），虽然问题不大，但是有一种更加完美的继承方式，也就是**寄生组合式继承**。

那么 寄生组合式继承是如何解决问题的呢：

+ 首先，还是使用**借用构造函数**来实现对超类型实例中属性和方法的继承。

+ 其次，使用**原型链的混成形式**来继承原型中的属性和方法。具体做法原理是使用寄生式继承来继承超类型的原型对象，将结果指定给子类型的原型，并且将``constructor``重新指会子类型。可以将这一过程封装成一个函数，并设置两个参数，一个是子类型，一个是超类型，如下：
```js
    function inheritPrototype(obj1, obj2) {
      var prototype = Object.create(obj2.prototype);  //创建原型对象
      prototype.constructor = obj1;                   //将constructor重新指向子类型
      obj1.prototype = prototype;                     //修改子类型的prototype
    }

```

使用 寄生组合式继承 来优化前面的组合继承的例子：
```js
    function Test1(money) {
      this.name1 = 'csz1';
      this.money = money;
      this.arr = [1, 2, 3, 4, 5, 6];
      Test1.prototype.name2 = 'csz2';
      Test1.prototype.fn = function () {
        return '这是test1';
      };
    }
    function Test2() {
      // 通过借用构造函数来继承实例中的属性和方法
      Test1.call(this, 999);
      this.name3 = 'csz3';
    }
    //通过原型链混成形式来继承超类型构的原型
    inheritPrototype(Test2, Test1);
    Test2.prototype.name4 = 'csz4';
    const obj1 = new Test2();
    const obj2 = new Test2();
    console.log(obj1.name2);//csz2
    console.log(obj1.money);//999
    obj1.arr.push(666);
    console.log(obj1.arr);// [1, 2, 3, 4, 5, 6, 666]
    console.log(obj2.arr);// [1, 2, 3, 4, 5, 6]
```
可以看出依然可以得出一样的理想的结果，但是通过这种形式就不存在着资源浪费的情况，是最完美的一种继承方式。

### 总结
这里着重介绍了继承的几种方式，以及优缺点，理解这六种继承方式是非常有必要的，以后就知道该使用哪种继承方式了，同时也对理解JS这门语言十分有帮助。


### 参考
《JavaScript高级程序设计(第三版)》