---
title: webpack的基本使用
date: 2019-07-25 22:43:46
tags:
 - webpack
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。这是webpack中文文档中的一段话。合理的使用好webpack可以使我们模块化开发以及后期的维护更加的便利，这也是webpack会是现在比较流行的打包工具之一，这篇博客就将介绍webpack的基本用法。
<!-- more -->
### npm的使用
如果对于npm还不是特别的熟练或者是了解可以先看一看这一部分的内容，来讲一讲npm的使用，npm是一款基于node.js的包管理工具，我们项目中用到的许多包都可以通过npm来安装到项目中然后加以使用，先来说一说简单的命令：

+ 首先是在项目中使用``npm init``来生成一个**package.json**这个配置文件中保存的是项目信息，也可以通过使用``npm init -y``来快速的生成一个package,但是要注意的一点是如果当前项目所在的文件名称中包含中文，那么就不能快速创建package文件，在手动配置时需要将项目名称命名为非中文的名称，这里简单的介绍npm，就不详细解释package中的内容了。

+ npm安装模块的基本语法：``npm install <Module Name>``，其中``install``可以简写为一个``i``，如果不加模块名称则安装所有package中保存有信息的包.

+ 可以通过``npm uninstall <Module Name>``来卸载已经安装模块。

+ 可以通过``npm update <Module Name>``来更新已经安装模块。

+ 可以使用``npm install <Module Name>  -g``来全局安装模块，没有``-g``则只是安装在项目中。

+ 可以通过``npm install npm -g``命令升级npm来保持npm是最新版本。

+ 切换包的仓库，默认的npm的包的仓库是在国外的，所以国内访问的话就会有较大的延迟，所以会导致下载速度较慢，这里提供一种较简单的切换包的仓库的办法：
``npm i nrm -g``安装**nrm**这样一个工具，然后通过``nrm ls``可以查看所有支持的包的仓库名称，通过``nrm use taobao``则可以切换仓库地址，这里就以淘宝镜像为例。这样下载速度就可以有很明显的提升。

+ ``npm install <Module Name>  -S``与``npm install <Module Name>  -D``的区别，首先弄懂这二者之间的区别要先了解到``-D``是``--save``的缩写，而 ``-D``是``--save-dev``的缩写，这二者之间的区别就在于``-S``会将包的信息保存到**package**的“dependencies”中，而``-D``则将保存到“devDependencies”中。而这二者之间的区别就在于如果是“dependencies”中的包是项目发布或者是运行时所依赖的包，而“devDependencies”中的包则是开发时说要用到的包。总的来说就是像jQuery这种在运行时就需要用到的包就需要通过``npm install <Module Name>  -S``来安装，而在开发过程中使用的工具，就像webpack这样的就是使用``npm install <Module Name>  -D``来安装，因为发布运行时并不依赖这些开发工具。

+ 这里在简单介绍一下npx 
npx 是在npm中的一种安装工具，在npm 5.2.0的版本的时候就已经开始默认安装了，那么这个工具究竟是干什么的呢。
  - 我们都知道可以全局安装和单独在项目中安装模块，但是只有全局安装时才可以直接使用模块提供的一些命令，如果只是在项目中安装的包则需要在**package.json**中配置**script**然后通过``npm run-script``的形式来执行命令。

  - 那么有了npx就可以不用这么麻烦了，直接通过``npx <command>``就可以直接执行本地的而非全局中的命令，并且如果项目中没有安装相应的包则会先帮你安装，十分的便利。

这里就介绍一些我平时可能用的比较多的一些命令，其他的就不详细介绍了，开始介绍webpack的使用。

### webpack的安装
+ 先在项目文件夹中初始化一下
```
npm init -y
```

+ 在创建两个文件夹dist和src,dist存放打包后的文件，src放源文件。

+ 安装webpack
```
$ npm i webpack webpack-cli -D
```
由于这里使用的webpack只是一个项目的构建工具，所以使用``-D``的形式来安装，并且由于后面一些插件需要项目本地安装webpack，所以这里就没有使用全局安装。


### webpack的初步使用
这里通过引入jQuery来简单的使用一下webpack：

+ 在src文件夹中创建两个文件index.html和index.js，并且引入：
```html
<body>
  <div class="test">这是一段文字</div>
  <script src="../dist/bundle.js"></script>
</body>
```

+ 这里以使用jQuery为例，如果我们不使用webpack进行打包，那么我们要使用jQuery就必须在页面中还要引入jQuery，这样会造成二次加载，造成不必要的资源浪费，所以我们可以使用ES6中的引入模块的语法，在index.js中引入jQuery模块。
  - 先安装jQuery，由于时运行时也需要的依赖，所以通过``-S``的方式来安装。
  ```
  $ npm i jquery -S
  ```

  - 引入jQuery，并且进行一些操作
  ```js
  import $ from 'jquery'
  $('.test').html('这是一段新的文字');
  ```

+ 但是通过上面这些操作是会直接报错的，因为浏览器目前还不支持这样的使用语法。这也是为什么要使用webpack的原因之一，通过webpack就可以将这文件打包，并且将其中的新语法进行转换，从而使得浏览器能够运行。
```
$ npx webpack ./src/index.js -o ./dist/bundle.js --mode development
(这里也可以配置package中的script来简化操作命令)
```
此时原来报错的js文件就打包好了，打包好的新的名为bundle.js的文件就存放在dist文件夹中，我们需要在html中引入新的js文件

>这里的mode可以不配置，默认时producition模式，后面可以在配置文件中进行配置。

+ 引入好打包好的js文件再次打开网页就可以发现并不会再报错了，并且可以正确的执行相关操作，这样就进行好了一次简单的打包。


### webpac配置文件
webpack的相关配置都是通过配置**webpack.config.js**这个文件来实现的，这个文件默认没有，需要自己手动的创建，并且放在项目的根目录中。

我们可以先做一下简单的配置：
```js
// 导入处理路径的模块
var path = require('path');
//配置信息
module.exports = {
  entry: path.resolve(__dirname, 'src/index.js'), // 项目入口文件
  output: { // 配置输出选项
    path: path.resolve(__dirname, 'dist'), // 配置输出的路径
    filename: 'bundle.js' // 配置输出的文件名
  },
  //配置模式：development、production none 三选一
  mode: 'development' //开发过程中使用development、发布时使用production。
}
```
通过这样简单的配置我们再次打包时所用的命令就会更加的简单了，因为配置都写在了配置文件中：
```
$ npx webpack
```
通过配置好相关的配置文件，就可以在执行命令的时候就不用再加上相应的配置了。

### 使用插件
通过上面的例子我们会发现这样打包其实也是不够便捷的，那么我们可以通过安装插件来使我们的使用更加的便捷。

#### webpack-dev-server
这款插件的作用就是不再需要我们自己手动的去打包，使用了这款插件以后当我们保存代码以后就会进行自动打包，使我们的开发更加的便利。

##### 安装
```
$ npm i webpack-dev-server -D
```

##### 使用
可以通过``$ npx webpack-dev-server``来启动服务

##### 配置
+ 首先我们可以通过配置package中的script来简化我们的命令：
```
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack-dev-server"
  }
```
这样设置以后我们就可以通过``npm run dev``来简化操作了。

+ 我们可以配置webpack-dev-server来更进一步的简化我们的操作，有两种方式：

方式一：
修改**package.josn**中的命令来配置
```
"dev": "webpack-dev-server --hot --port 4321 --open --contentBase src"
```
这里解释一下这些配置都是什么含义：
  - ``--hot`` 启用热更新

  - ``--port 4321``修改端口为4321

  - ``--open`` 自动打开浏览器

  - ``--contentBase src``默认打开的是src文件夹而不是项目跟路径

方式二：
配置**webpack.config.js**
1. 头部引入webpack包：
```
const webpack = require('webpack');
```

2. 在module.exports新增配置信息：
```js
  devServer: {
    hot: true,
    open: true,
    port: 4321,
    contentBase: 'src'
  }
```

3. 在 plugins 数组中新增：
```
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ],
```

##### 问题
这样配置好后再继续使用确实是会简便很多，但是启动之后我们还是能够发现一个问题，那就是似乎启动之后的页面并没有得到我们想要的结果，那么问题处在哪里呢，其实这是因为通过**webpack-dev-server**启动打包的js文件并不是我们手动打包生成的那个js文件，并且原来我们手动打包生成的那个js文件也已经无效了，而通过 webpack-dev-server 打包生成的js文件是存放在内存中的，路径是在项目的根路径中，所以解决这个问题的方法有三种。

1. 修改网页的引用路径：
```html
  <script src="/bundle.js"></script>
```

2. 修改 webpack-dev-server 配置
```
  devServer: {
    publicPath: "/dist/",
  },
```

3. 使用 html-webpack-plugin 插件
后面详细介绍这款插件，这里就不做过多介绍。

#### html-webpack-plugin
前面说到了在使用 webpack-dev-server 出现的问题，也提到了这个问题也可以通过 **html-webpack-plugin** 这款插件来解决，那么来看看如何使用这款插件：

##### 安装
```
$ npm i html-webpack-plugin -D
```


##### 配置
+ 导入模块
```
const htmlWebpackPlugin = require('html-webpack-plugin');
```

+ 添加到 plugins 数组中
```
  plugins: [
    new htmlWebpackPlugin({
      template: path.resolve(__dirname, 'src/index.html'),//源文件路径
      filename: 'index.html'//自动生成的HTML文件的名称
    })
  ],
```

配置好以后就可以使用了，那么这款插件是起什么作用的呢，前面提到了 webpack-dev-server 插件会将js文件生成到根路径内存中，那么 html-webpack-plugin 这款插件也是类似的，将html文件生成到内存中，并且会将页面中原本对js的引用失效，换成对内存中在根路径的js文件的引用，刚好也解决了前面插件的问题，同时存放在内存中也能够提高运行速度，并且会在output.path的路径中生成一份由我们命名的html文件，方便管理，并且这个新生成的文件会自动引入入口js文件，所以在源文件中就要把原来对入口文件的引用去掉。

### loaders
loader 用于对模块的源代码进行转换。当源代码中引入了非js模块的时候就有可能会出现无法打包的情况，这是因为webpack默认是无法处理除了js之外的文件的，那么此时就要用到各种各样的loader来帮助我们处理这些文件或者模块了。

#### css-loader
在一个前端项目中我们除了js之外最长用到的应该就是css文件了，如果直接在入口文件中引入css文件的话在打包的时候就会报错，这是后就需要用相应的loader来处理了。

像是这样直接使用就会报错：
```js
import $ from 'jquery'
import './index.css' 
$('.test').html('这是一段新的文字');
```

那么来看看如何使用 css loader：

1. 先是通过``$ npm i style-loader css-loader -D``来安装两个需要的loader

2. 然后再到配置文件中进行相应的配置：
```js
  module: { // 用来配置第三方loader模块
    rules: [ // 匹配规则
      { test: /\.css$/, use: ['style-loader', 'css-loader'] }//处理css文件的规则
    ]
  },
```
>test 就是相应要匹配的文件，这里使用的是正则表达式，表示匹配的是以 .css 结尾的文件。use是一个表示要用什么loader来处理前面 test 匹配的文件。

#### less-loader
less-loader 与css-loader类似，只不过是用来处理less文件的：

1. 通过``npm i less-loader less -D`` 来安装。

2.设置匹配规则：
```
{ test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] },
```

#### sass-loader
sass-loader 与css-loader类似，只不过是用来处理scss文件的：

1. 通过``npm i sass-loader node-sass --D``

2.设置匹配规则：
```
{ test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader'] }
```

#### file-loader
当我们通过url地址引用一些图片或者是字体图标的时候，webpack默认也是处理不了的，这个时候我们就需要使用到**file-loader**了：

比如在css中设置背景图片时：
```css
.test {
  color: aqua;
  width: 400px;
  height: 400px;
  background: url(./images/1.png);
}
```

使用 file-loader ：

1. 通过``$ npm i file-loader -D``安装

2.设置匹配规则：
```js
{ test: /\.(png|jpg|gif)$/, use: 'file-loader?name=[name].[ext]' },   //匹配图片，并且设置参数图片名称为图片原本名称加上原本后缀名
{ test: /\.(ttf|eot|svg|woff|woff2)$/, use: 'file-loader' },//匹配字体
```
可以像这样设置参数，具体有哪些选项并且有什么作用可以看<a href="https://www.webpackjs.com/loaders/file-loader/">这里</a>。

#### 其他loader
还有其他的一些loader这里就不一一介绍了，在有需要的情况下可以选择安装，这里是<a href="https://www.webpackjs.com/loaders/">官方文档</a>。

### 总结
webpack是一款非常优秀的打包工具，这篇博客的例子非常简单，只演示了单入口单页面的情况，但是我们已经可以看出使用webpack带来的便利了，所有的资源全部被打包在了``output.path``目录中，也可以通过配置**file-loader**将图片之类的资源放在单独的子目录中在图片多的情况下方便管理。

最后再讲一下publicPath，这个的作用就是设置文件最终输出时的相对路径前面的路径。


