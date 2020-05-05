---
title: sass的简单使用
date: 2019-08-29 16:06:02
tags:
 - CSS
 - Sass
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sass是一门css的扩展性语言，起初在没有使用sass之前，我写css其实并不是一件让人很舒服的过程，因为写的过程经常要写很多重复的代码，父级选择器也要一直复制来复制去，也不是很好维护和管理，直到我认识了sass，第一次听说css扩展语言还是在bootstrap官网，看到说bootstrap4使用sass替代了less，才知道css还有扩展语言这么一回事，特别是当上手开始使用sass之后真的是会让人感觉特别的惊喜，因为写起来太舒服了。
<!-- more -->
### 安装
虽然中文文档中有安装的教程，但是还是要另外提一下，那个太久了，如果完全按照那个来的话最终是安装不了的。

sass 是基于 ruby 开发的，所以安装 sass 之前需要先安装 ruby，直接到<a href="https://rubyinstaller.org/downloads/">官网</a>下载安装即可（记得添加进PATH）。 

通过命令行查看版本，如果出现版本信息则安装成功：
```shell
$ ruby -v
$ ruby 2.5.5p157 (2019-03-15 revision 67260) [x64-mingw32]
```

然后是替换gem源，换成速度更快的国内的源：
```shell
$ gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/
$ gem sources -l
*** CURRENT SOURCES ***

https://gems.ruby-china.org
# 请确保只有 gems.ruby-china.org
```
>与中文文档有区别的也就是这里，中文文档中的国内镜像的地址已经不维护使用了，所以要用新的地址。

然后就可以开始安装使用sass了
```shell
$ gem install sass
$ gem install compass
```

具体的使用方法可以参考<a href="https://www.sass.hk/install/">中文网</a> ，因为通常都是在编辑器中配合插件使用，我是使用vscode配合Easy Sass插件进行自动编译，所以这里就不做过多的介绍了。

### sass中比较常用的一些特性
在sass中有非常多的功能与特性，我在这里只介绍一些比较常用的特性，光是使用这些特性就已经能够使书写css样式轻松非常的多了。

#### 嵌套
我之前没有接触过css的扩展语言，当我第一次接触到sass的嵌套的时候我就觉得这个太方便了，因为之前写css总要经常性的重复书写父级的选择器，这是很麻烦的一件事情，但是在sass中可以直接嵌套书写：
+ 在css中,我们经常需要重复写父级：
```css
.main .left {
  left: 100px;
}
.main .right {
  right: 100px;
}
```

+ 但是在sass中，直接嵌套在其中就可以了：
```css
.main {
  width: 800px;
  height: 600px;
  background-color: antiquewhite;
  margin: 0 auto;
  position: relative;

  .left {
    @extend .position;
    left: $bj;
  }

  .right {
    @extend .position;
    right: $bj;
  }
}
```
可以看出这样使非常的方便，并且我认为这不仅仅是使我们少写了很多的css重复代码，这还使我们的css结构看起来更加的清晰，子级与父级的关系一目了然，并且因为有了这样清晰的结构，所以在vscode的大纲中的结构也是一目了然，这使得后期维护变得非常的方便，再也不用担心找不到想找的样式的代码在哪里了。我觉得光这一个特性就已经使书写css的过程发生质变了。

这样嵌套生成的是后代选择器，如果要嵌套出子代选择器或者兄弟选择器只需要在前面加上相应的符号就好了：
```css
.main {
  width: 800px;
  height: 600px;
  background-color: antiquewhite;
  margin: 0 auto;
  position: relative;

  > .left {
    @extend .position;
    left: $bj;
  }
}
```

可以用``&``替代父级，比如说用到hover的时候：
```css
.main {
  :hover {
    color: aqua;
  }
}
```
直接这样写是不能如我们所想的那样解析的，需要像这样写，用``&``代替父级：
```css
```css
.main {
  &:hover {
    color: aqua;
  }
}
```

可以看一下，通过查看大纲就可以很轻松的定位相应css的位置了：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/wALp2JVAFMoCH3xzdD3h5HSfGr0zJ990HHgKXQ.x3PM!/b/dFQBAAAAAAAA&bo=NgGuAAAAAAADB7s!&rf=viewer_4">

这里再提一下其实不仅仅是选择器可以进行嵌套，其实属性也是可以嵌套的，但是因为可能书写起来不是那么的方便并且使用场景也相对较少所以就不多提了。


#### 变量
sass中的变量也是一个能够提升开发维护效率的一个特性，因为我们经常要对很多的选择器设置一样的属性，通常都是通过复制粘贴来实现，这不方便并且不便于维护，当要修改时全部都得改一遍十分的麻烦，通过再sass中使用变量就可以解决这个问题。

变量的使用也是十分的简单，通过``$``来设置变量名，就像这样：
```css
  $bj:100px;

  .left {
    @extend .position;
    left: $bj;
  }

  .right {
    @extend .position;
    right: $bj;
  }
```
这样只要是用到了变量的地方就都会被变量的属性值替代了，只要修改了变量，所有用到了相应变量的地方也会一起随着变化，所以这会是维护变得极其的简单。

要注意的一点是变量只在定义的相应的区域内有效，超出了范围就没有效果了。

#### 混合器
我的理解是混合器更像是大段的变量一样，和变量类似，可以用来设定那些会重复使用的代码片段，并且还可以传递参数用来进行细微的区分。

比如说我这里有两个小块，和一个大块，两个小块在大块中，区别就是一个在左，一个在右，剩下的都一样，这个时候我们就可以使用混合器来复用代码了:
```css
.main {
  width: 800px;
  height: 600px;
  background-color: antiquewhite;
  margin: 0 auto;
  position: relative;

//将通用的代码提取出来
  @mixin position {
    position: absolute;
    top: 50%;
    transform: translateX(-50%);
    -webkit-transform: translateX(-50%);
    -moz-transform: translateX(-50%);
    -ms-transform: translateX(-50%);
    -o-transform: translateX(-50%);
  }

//通过@include引入
  .left {
    @include position;
    left: 100px;
  }

  .right {
    @include position;
    right: 100px;
  }
}
```
这样就实现了代码复用，对于这些公用的样式以后直接修改混合器中的内容就可以了

甚至还可以通过使用参数来进一步的简化：
```css
.main {
  width: 800px;
  height: 600px;
  background-color: antiquewhite;
  margin: 0 auto;
  position: relative;

  //可以通过键值对的形式来设置默认参数
  @mixin position ($l:unset, $r:unset) {
    position: absolute;
    top: 50%;
    transform: translateX(-50%);
    -webkit-transform: translateX(-50%);
    -moz-transform: translateX(-50%);
    -ms-transform: translateX(-50%);
    -o-transform: translateX(-50%);
    left: $l;
    right: $r;
  }

  .left {
    @include position(100px, unset);
  }

  .right {
    @include position(unset, 100px);
  }
}
```
可以看出通过参数就可以完成对不一样的地方进行设置。

这里再来说一下混合器的实现原理，原理其实是代码的复制，也就是将混合器中的代码复制到包含这个混合器的选择器中。缺点可能就是编译后的css文件会有比较多的代码。

来看下效果：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/uuKElZCTeEIEHbXsDovri2dHVpsxQ6Ci0bOC2rZTD.4!/b/dL4AAAAAAAAA&bo=fQcEAwAAAAADB18!&rf=viewer_4">

#### 继承
继承的就是我们理解的那个继承，可以在一个选择其中继承另一个选择器的全部内容（包括与其相关的选择器），继承的作用与用法与混合器有些相似，但是区别在于继承的一定也是一个选择器并非一个单独的代码段，在实现的原理方面，混合器是单纯的复制代码，而继承则是复制选择器，可以将上面的例子通过继承来实现：
```css
.main {
  width: 800px;
  height: 600px;
  background-color: antiquewhite;
  margin: 0 auto;
  position: relative;

  //定义一个要继承的类，相当于如果我们不使用继承，而使用两个类，那么这个就是基类
  .position {
    position: absolute;
    top: 50%;
    transform: translateX(-50%);
    -webkit-transform: translateX(-50%);
    -moz-transform: translateX(-50%);
    -ms-transform: translateX(-50%);
    -o-transform: translateX(-50%);
  }

  $bj:100px;

  .left {
    @extend .position;
    left: $bj;
  }

  .right {
    @extend .position;
    right: $bj;
  }
}
```
其实相较于使用混合器，我更喜欢使用继承来实现复用，因为这样编译后的代码更加的少，但是混合器的优势也很明显，可以参数。

>你完全可以放心地继承有后代选择器修饰规则的选择器，不管后代选择器多长，但有一个前提就是，不要用后代选择器去继承。

#### 导入样式文件
css提供的导入css文件的方式速度比较慢，所以sass中还提供了一种导入sass文件的方式，当然也可以导入css文件只不过这样做的话采用的还是css的那种方式，不过其实现在都是采用打包工具进行开发在页面入口文件中导入不同的样式文件，都不会在css中去导入样式文件，不过还是要简单的提一下。

在sass中可以通过 ``@import``来导入样式sass文件，并且可以不需要添加后缀名，比如
```css
@import "content"
```

也可以导入部分的sass文件，比如一个sass文件只是需要的部分代码，那么它的文件名就可以以``_``开头，这样做的话就不会被编译了，导入的时候也可以省略前面的``_``。

导入css文件的话只需要上css后缀就可以，不过这种方式还是通过css的比较慢的方式来导入：
```
@import "content2.css"
```


### 总结
sass有着众多强大的特性，那怕仅使用我这篇文章提到的特性都已经可以大大提升开发效率以及维护的效率了，太好用了，熟练掌握之后就可以愉快的书写样式了！
