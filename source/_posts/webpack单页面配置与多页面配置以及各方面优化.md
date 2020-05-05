---
title: webpack单页面配置与多页面配置以及各方面优化
date: 2019-08-26 18:01:22
tags:
 - webpack
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这几天都在琢磨着配置好webpack的多页面配置以及优化，原本只是其实配置过一份简单的版本，但是对webpack其实也不是特别的熟悉，所以这次配置多页面以及优化还是踩了不少坑的，不过总之是配置好了一份自己满意的配置，可以愉快的用webpakc来打包项目了。
<!-- more -->
### webpack单页面配置
其实在之前开始学习webpack的时候就已经配置好了一份简单的webpack单页面的配置，但是当时也只是简单的配置，没有考虑到性能的优化之类，所以不是特别的满意，目录结构考虑的也不是很周到，对webpack的理解也非常的浅，然后现在的这份webpack配置就是我目前很理想的配置了。

#### 目录结构
可以先来看一下项目最终打包好的目录结构：
```
│  package-lock.json
│  package.json
│  webpack.common.js
│  webpack.dev.js
│  webpack.prod.js
│
├─dist
│  │  index.34b749f14486ceb6af7a.js
│  │  index.html
│  │  index9e2d405ad193f7d585f9.css
│  │
│  └─aseets
│      └─images
│              92dcab1797689d6d87ef20dd22c47be6.png
│              a7a91ea31281adbf0ea7a3213fdd6f96.png
│              b88a75406eaf6c2f133482b0f4b56833.png
│              e07b8dbff850e41c69522320b0c5ea87.png
│              fbcc569b7c705a3c80d2fa95e3a17226.png
│
└─src
    │  index.css
    │  index.html
    │  index.js
    │  index.min.css
    │  index.scss
    │
    └─aseets
        ├─fonts
        └─images
                1.png
                2.png
                3.png
                4.png
                strawberry.png
```
这个是我个人比较习惯的一个路径，在单页面的时候路径就简单的多了。

#### 配置文件详细信息
在之前我是只是用一个配置文件的，可是后来发现开发环境和生产环境的差别确实很大，需要分别配置，这里我是使用了一款叫做**webpack-merge**的插件，这也是官方文档中使用的插件，需要了解详细的用法的话，也可以前往<a href="https://webpack.js.org/configuration/#use-different-config-file">官方文档</a>进行查看。

这里通过插件将配置文件拆分成三部分，分别是公用配置、开发环境配置、生产环境配置，分别如下：
##### webpack.common.js
```js
const path = require('path');
const htmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

//配置信息
module.exports = {

  entry: {// 项目入口文件
    index: './src/index.js',
  },
  output: { // 配置输出选项
    path: path.resolve(__dirname, 'dist'), // 配置输出的路径
    // filename: '[name].js', // 分别配置dev与prod
  },

  module: {
    noParse: [/jquery/, /react\.min\.js$/],  //当webpack打包时忽略这些文件，因为没有采用模块化开发，同时建议通过cdn的方式来引入非模块化的文件
    rules: [ // 匹配规则

      //匹配图片
      {
        test: /\.(png|jpg|gif)$/,
        use: ['url-loader?outputPath=aseets/images&limit=8192&name=[contenthash].[ext]&fallback=file-loader']
      },

      //匹配字体
      {
        test: /\.(ttf|eot|svg|woff|woff2)$/,
        use: ['file-loader?outputPath=aseets/fonts']
      },

      // 抽离css并压缩
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'fast-css-loader']
      },

      {
        test: /\.(sass|scss)$/,
        use: [MiniCssExtractPlugin.loader, 'fast-css-loader', 'fast-sass-loader']
      }

    ],
  },

  // 通过externals可以检索外部依赖，而不需要本地依赖也能通过 import引用。
  externals: {
    echarts: 'echarts',
    jquery: 'jQuery',
  },

  //抽出第三方库文件
  optimization: {
    // runtimeChunk: 'single',
    splitChunks: {
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all'
        }
      }
    }
  },

  plugins: [

    new htmlWebpackPlugin({
      template: path.resolve(__dirname, 'src/index.html'),//圆文件路径
      filename: 'index.html'//自动生成的HTML文件的名称
    }),

    // 抽离css
    new MiniCssExtractPlugin({
      filename: '[name][contenthash].css'
    }),

  ],

}
```
这里的配置比较简单都是一些常用的配置及插件配置，这里需要注意的一件事就是因为在开发环境中我不需要使用到 [contenthash] 来为入口文件命名，所以``output.path``这部分我在其他两份配置文件进行了配置。

##### webpack.dev.js
```js
const merge = require('webpack-merge');
const common = require('./webpack.common.js');
const webpack = require('webpack');

//配置信息
module.exports = merge(common, {
  mode: 'development',
  output: { // 配置输出选项
    filename: '[name].js', // 配置输出的文件名
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
  ],
  devServer: {
    hot: true,
    open: true,
  },
})
```
开发环境需要单独配置的很少，主要就是热更新的配置。

##### webpack.prod.js
```js
const merge = require('webpack-merge');
const common = require('./webpack.common.js');
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const TerserJSPlugin = require('terser-webpack-plugin');

module.exports = merge(common, {

  mode: 'production',

  output: { // 配置输出选项
    filename: '[name].[contenthash].js', // 配置输出的文件名
  },

  // 压缩js与css
  optimization: {
    minimizer: [
      new TerserJSPlugin({
        cache: true,
        parallel: true,
      }),
      new OptimizeCSSAssetsPlugin({})
    ],
  },

  plugins: [
    new CleanWebpackPlugin(),
  ],
});
```
生产环境的配置就相对多一些了，主要就是打包清理的插件以及压缩的插件。


**在两个环境的配置文件配置好之后就可以用起来了，这里要注意的是现在这样是不能直接拿来用的，我刚开始就是忽略了这点，想要生效还需要在打包或者通过 dev 预览的时候指定配置文件就行，我是通过pakage.script来指定的，像是这样：**
```shell
  "scripts": {
    "start": "webpack-dev-server --config webpack.dev.js",
    "build": "webpack --config  webpack.prod.js"
  },
```

单页面的配置就这样配置好了，我做的优化以及插件相关的问题我会到优化部分再说。

### webpack多页面配置
上面是单页面的配置，那么我们肯定不会只进行单页面的开发，多页面的开发也是非常常见的，多页面的配置就会相比要复杂一些，因为页面更加的多。

#### 目录结构
这是我多页面的页面结构，index.html的位置还是一样的，将其他的所有页面进一个文件夹内，css、js也是同理：
```
│  package-lock.json
│  package.json
│  webpack.common.js
│  webpack.dev.js
│  webpack.prod.js
│
├─dist
│  │  index.html
│  │
│  ├─pages
│  │      page1.html
│  │      page2.html
│  │
│  └─static
│      └─js
│              index.acb8d4a0a354c09aa1a5.js
│              page1.08868399604c484bbd2a.js
│              page2.ab4baec0ced2346793dd.js
│
└─src
    │  index.html
    │
    ├─aseets
    │  ├─fonts
    │  └─images
    │          bb.png
    │          cm.png
    │
    ├─pages
    │      page1.html
    │      page2.html
    │
    └─static
        ├─css
        │      index.scss
        │      page1.scss
        │      page2.scss
        │
        └─js
                index.js
                page1.js
                page2.js
```
以上就是我多页面的配置文件，可以看出单页面与多页面在目录的结构方面差别还是很大的。

#### 配置文件详细信息
#### dev 和 prod 
因为基本上没有什么太大的变化就合在一起讲一下变化吧：

+ 仔细一看其实没有什么大变化

+ 唯一的变化也就是因为是多页面，所以在使用 **devServer** 的时候要更改一下默认的打开路径，方便管理其他页面：
```js
  devServer: {
    hot: true,
    open: true,
    openPage: '/pages',
    contentBase: 'src',
  },
```

##### webpack.common.js
变化比较大的也就是这份common了，其实变化大主要是要修改多页面以及这些入口文件的输出位置。

由于页面的数量不是固定的而且我也不想每次都去修改配置文件，所以我采用了动态获取所有的页面并且将其入口文件进行打包到指定文件夹中，这样进行开发的时候就比较省事了。

这是配置文件：
```js
const path = require('path');
const htmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const glob = require('glob');

// 生成所页面的入口文件
function entries() {
  const jsDir = path.resolve(__dirname, 'src/static/js');
  const entryFiles = glob.sync(jsDir + '/*.js');
  let map = {};
  for (let i = 0; i < entryFiles.length; i++) {
    let filePath = entryFiles[i];
    let filename = filePath.substring(filePath.lastIndexOf('\/') + 1, filePath.lastIndexOf('.'));
    map[filename] = filePath;
  }
  return map;
}

//生成pages中的所有htmlWebpackPlugin的配置信息
let pluginarr = [];
let HWP = function () {
  let conf, filename;
  const htmlDir = path.resolve(__dirname, 'src/pages');
  const htmlFiles = glob.sync(htmlDir + '/*.html');
  for (let i = 0; i < htmlFiles.length; i++) {
    filename = htmlFiles[i].substring(htmlFiles[i].lastIndexOf('\/') + 1, htmlFiles[i].lastIndexOf('.'));
    conf = {
      template: path.resolve(__dirname, 'src/pages/' + filename + '.html'),//圆文件路径
      filename: path.resolve(__dirname, 'dist/pages/' + filename + '.html'),//自动生成的HTML文件的名称
      title: filename,
      chunks: [filename],
    }
    pluginarr.push(new htmlWebpackPlugin(conf));
  }
}
HWP();



//配置信息
module.exports = {

  entry: entries(),
  output: { // 配置输出选项
    path: path.resolve(__dirname, 'dist/static/js'), // 配置输出的路径
    // filename: '[name].js', // 分别配置dev与prod
  },

  module: {
    noParse: [/jquery/, /react\.min\.js$/],  //当webpack打包时忽略这些文件，因为没有采用模块化开发，同时建议通过cdn的方式来引入非模块化的文件
    rules: [ // 匹配规则

      //匹配图片
      {
        test: /\.(png|jpg|gif)$/,
        use: ['url-loader?outputPath=aseets/images&limit=8192&name=[contenthash].[ext]&fallback=file-loader']
      },

      //匹配字体
      {
        test: /\.(ttf|eot|svg|woff|woff2)$/,
        use: ['file-loader?outputPath=fonts']
      },

      // 抽离css并压缩
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'fast-css-loader']
      },

      {
        test: /\.(sass|scss)$/,
        use: [MiniCssExtractPlugin.loader, 'fast-css-loader', 'fast-sass-loader']
      }

    ],
  },

  // 通过externals可以检索外部依赖，而不需要本地依赖也能通过 import引用。
  externals: {
    echarts: 'echarts',
    jquery: 'jQuery',
  },

  //抽出第三方库文件
  optimization: {
    // runtimeChunk: 'single',
    splitChunks: {
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all'
        }
      }
    }
  },

  plugins: [

    new htmlWebpackPlugin({
      template: path.resolve(__dirname, 'src/index.html'),//圆文件路径
      filename: path.resolve(__dirname, 'dist/index.html'),//自动生成的HTML文件的名称
      title: 'index',
      chunks: ['index'],
    }),

    ...pluginarr,

    // 抽离css
    new MiniCssExtractPlugin({
      filename: '[name][contenthash].css'
    }),

  ]
}
```
这里说一下我在写这些配置的时候遇到的一些困难：

+ 首先是因为对node.js不了解，所以在参照别人进行动态的获取指定文件夹内的所有文件的文件名的时候遇到了一些困难，然后编写函数的时候也会困难一些，因为不知道那些node中的函数是什么意思，后来把这些不认识的node函数都查了一遍就理解了，从而实现了遍历所有的入口文件，也就是这部分：
```js
function entries() {
  const jsDir = path.resolve(__dirname, 'src/static/js');
  const entryFiles = glob.sync(jsDir + '/*.js');
  let map = {};
  for (let i = 0; i < entryFiles.length; i++) {
    let filePath = entryFiles[i];
    let filename = filePath.substring(filePath.lastIndexOf('\/') + 1, filePath.lastIndexOf('.'));
    map[filename] = filePath;
  }
  return map;
}

//然后将函数返回的对象传递给entry就行了。
entry: entries(),
```
动态配置``htmlWebpackPlugin``也是类似，不过会稍微麻烦一点，原理是一样的。

+ 遇到的第二个比较大的困难就是网上的资料不是特别的齐全与准确，也花费了我比较多的时间
首先是因为不了解node，所以不知道还需要先安装``glob``这个模块才能使用，一开始还以为是自带的不用安装，然后就是参照别人的动态配置``htmlWebpackPlugin``的函数，然后写了自己的函数，但是花了很多的时间总是的不到我想要的效果，最后发现最坑的是在有一处需要写``htmlWebpackPlugin``这段代码，我肯定是选择复制一下会更加的方便，结果我从别人的代码里复制，结果复制来的是``HtmlWebpackPlugin``,可能很多人这样看都没有看出什么问题，我一开始也没意识到问题的严重性，但是又总是报错``HtmlWebpackPlugin``没有定义，我心里那个气啊，不是定义了吗？怎么就没定义了，知道后来我发现我的代码里面怎么有一个叛徒``HtmlWebpackPlugin``???，这个开头的字母错了啊，我的天啊，困扰我这么久的竟然是这个问题，我竟然复制的别人的是错了，我真不明白那个老哥的配置是怎么生效的，我崩溃啊，好在问题解决了。

+ 后来又遇到的一个比较严重的问题就是对node中的``__dirname``理解的不对，这导致我在截取不带后缀的文件名的时候出现了问题，后面意识到了问题，进行了改正。

+ 还有一个不够细心造成的问题，在配置没有完全配置好之前我进行了一次测试性的打包，导致将 index.html 打包进了 src 目录中，这不是问题的重点，重点是，这个文件中注入了三个入口js，所以导致我在后面打包的时候无论怎么修改配置，想尽办法，打包出来的 index.html 都会带有四个入口文件，我实在是没想明白怎么回事，头疼，后面经过排查，最终排查到src下的index.html，最终发现了问题。

### webpack优化
前面只是展示了一下单页面与多页面的配置，插件具体的作用以及我所作的优化都没有详细进行介绍，这里来专门讲一下我在webpack的打包与代码质量方面所做的优化。我将这些优化分为两部分，一是优化之后提高打包速度，二是优化之后提升用户体验。

#### 提升打包速度
#### 缩小webpack打包时的解析范围
对于那些非模块化开发的库文件可以通过 module.noParse 字段来将其排除在webpack的解析范围之外。像是jQuery和echarts这种，因为这些本身就是非模块化开发的，所以没必要浪费资源进行解析。我的配置文件是这样设置的：
```js
 noParse: [/jquery/, /react\.min\.js$/],
```
其实除了这样处理可以缩小解析范围，大部分loader也可以通过配置来缩小解析范围。

#### 使用速度跟快的loader
部分官方loader有时存在bug的，并且速度也不是最快的，这里我使用了两款关于css的loader，分别时 fast-css-loader 以及 fast-sass-loader ，使用方法基本上和普通版是一样的，详细的使用办法可以看这篇<a href="https://segmentfault.com/a/1190000014740483">文章</a>

#### 使用happypack
webpack原本的打包过程是单线程的，通过这款插件可以使各loader的打包过程变为多线程的，其实我的配置文件中并没有使用它，因为对 file-loader 和 url-loader 支持的不好，而且我还使用了抽离css的插件导致css-loader也不支持了，也就babel-loader有用了，但是我基本上不写需要balel的代码，用不到，所以就不做过多介绍了。

#### 提升用户体验
#### 使用cdn
通过将静态资源（除了html文件）存放到cdn中并且开启缓存来对资源的加载进行加速。这里要注意的必须是文件名带上内容计算出的hash，不能能直接使用hash来进行缓存，一定要使用内容计算出的hash，也就是 contenthash 来进行缓存（通过看我的配置文件可以看出我是使用了contenthash），因为如果直接使用hash的话就会导致每次打包的时候所有文件都会重新生成hash，那么这样生成的hash就没有意义了。

#### 非模块化库使用cdn
如果没有选择将大部分资源放到cdn中的话，对于那些非模块话开发的库文件，可以通过cdn来将其引入，这样可以减少打包后的体积，并且也可以使这些文件的加载速度变快（这样处理之后其实也就没必要骗用到上面的优化办法了，也看具体情况吧）。

这里再补充一下如果这时还需要通过引入模块的方式来引入这些通过cdn引入的文件的话该怎么办，可以通过 module.externals 来解决，像是我这样：
```js
  externals: {
    echarts: 'echarts',
    jquery: 'jQuery',
  },
```
属性名 jquery是表示  import $ from 'jquery'  应该排除jquery模块,而属性值jQuery 表示将检索一个 jQuery 来当作全局变量。这样就可以通过引入模块的方式来继续使用这些库了。


#### 抽离css
抽离的好处不用注入样式，减少性能开销，可以对样式进行压缩，减少代码大小。

可以通过  mini-css-extract-plugin 插件来实现，可以参考我的配置文件中的关于  mini-css-extract-plugin 插件的配置信息。

抽离之后记得取消使用 style-loader 。

#### 压缩代码
我使用的是webpack4.x的版本，已经是默认再生产模式下是自动压缩js与css文件了。

但是这里还是要提一下如何手动配置对js或者是css文件进行压缩，我这里是配置了的，我为什么要配置呢，其实主要是因为我在前面提到了我是使用了 fast-css-loader 的，使用这个的话是默认没有压缩功能的，是的，只有官方的 css-loader才有压缩的功能，那我们手动配置的话只需要使用 OptimizeCSSAssetsPlugin 这个插件，具体的配置方法也可以参考我的配置文件。

还要注意的是如果使用了这个插件进行css代码的压缩，那么js文件的压缩功能会被顶掉，所以我们就还需要安装插件进行js文件的压缩，我这里使用的是 TerserJSPlugin 使用方法参考我的配置文件。也有其他的压缩插件，这里就不介绍了。

当然，如果不使用fast-css-loader就不必要使用额外的压缩插件，具体怎么选择就仁者见仁了。

#### 使用url-loader
对于图片的处理来说配置url-loader也有一定的提升，url-loader相较于file-loader来说比较明显的区别就在于可以将图片转化为base64格式的。

这里要注意的是通过base64的转化，图片的大小会增加三分之一左右，但是可以减少一次网络请求，所以设置将多大的图片转化为base64就尤为重要了：
```js
      { 
        test: /\.(png|jpg|gif)$/,
        use: ['url-loader?outputPath=aseets/images&limit=8192&name=[contenthash].[ext]&fallback=file-loader']
      },
```
我这里设置的是将小于8192比特的图片转化为base64，也可以根据项目情况进行调整。大于这个数值的默认就会调用file-loader来进行处理。

#### 懒加载
懒加载就是在用户需要时再按需引入相应的模块，这样可以减少首次加载的时间，合理的使用资源，具体可以看webpack官方文档的简单的<a href="https://webpack.js.org/guides/lazy-loading/">例子</a>。

#### 多页面抽离公共代码
在webpack4.x中不再需要特别区配置这部分的东西了，因为自带了这方面的插件并且使用默认配置也是足够的，如果要看具体怎么配置的话也可以看<a href="https://webpack.js.org/configuration/optimization/">官方文档</a>的介绍。

原理就是多页面中会有很多重复用到的代码，比如说第三方库之类的，这类资源基本上不会被修改，所以可以把这些相同的代码抽离出来，当用户第一次请求这些公共部分的文件时，就会被浏览器缓存，当其他页面要使用的时候就不需要再次进行请求了，直接使用缓存就可以了。

### 总结
从网上找各种资料到引用到配置适合自己的配置这个过程还是花了很多的时间的，也踩了很多的坑，但是终究还是配置好了，也让我对webpack有了更多的认识，也更加的发觉了使用webpack的好处，我将会把这两份代码配置放到github中，要使用的时候直接clone下来就好了，特别是再给命令起个别名，用起来就很舒服了。

