---
title: 通过百度API添加搜索小功能
date: 2019-08-10 21:34:07
tags: 
 - JS
 - chrome插件开发
 - chrome
categories:
 - 页面小功能
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这两天在开发一个自己的chrome插件，实现自定义新标签页，那么新标签页肯定少不了搜索的功能，因为我比较常用的搜索引擎是百度，所以会跳到百度的搜索结果，并且通过调用API来实现有一个提示的小窗口，原理是挺简单的，但是写的过程中还是遇到一些问题，记录一下。
<!-- more -->
### 先来看看效果图
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/8oA8YneY5riROKOZYZesXy7dwsX0xuZjTV4mpHJGPYk!/b/dFEBAAAAAAAA&bo=DwaIAwAAAAADB6A!&rf=viewer_4">
做的不是很好看，因为目前只是把功能实现了，样式都还是测试用的。重点来讲讲功能。

### 实现代码

#### HTML代码
```html
  <div class="main">
    <div class="centerBox">
      <div class="log"></div>
      <div class="searchWrap">
        <svg class="icon sousuo" aria-hidden="true">
          <use xlink:href="#icon-sousuo"></use>
        </svg>
        <input type="text" class="searchInput">
      </div>
      <ul class="searchTips">
      </ul>
    </div>
  </div>
```

#### JS代码：
```js
 <script src="./asset/font/iconfont.js"></script>
  <script src="./asset/js/jquery-3.4.1.min.js"></script>
  <script>
    $(function () {

      // 百度搜索
      {
        // 搜索部分
        const searchInput = $('.searchInput');
        const searchTips = $('.searchTips');
        let tipItems = searchTips.children();
        let liList = ``;
        let query = searchInput.val();
        let timer;
        let tipIndex = -1;
        searchInput.keyup(function (e) {
          // 按下回车进行搜索
          if (e.keyCode == 13) {
            query = $(this).val();
            window.open(`https://www.baidu.com/s?ie=UTF-8&wd=${query}`);
          }
        })

        // 提示部分
        searchInput.on('input', function () {
          tipIndex = -1;
          let query = searchInput.val();
          const url = "https://sp0.baidu.com/5a1Fazu8AA54nxGko9WTAnF6hhy/su?wd=" + query + "&cb=?";
          $.getJSON(url, function (data) {
            console.log(data.s);
            data.s.forEach(item => {
              liList += `<li>${item}</li>`;
            });
            searchTips.html(liList);
            liList = ``;
            tipItems = searchTips.children();

            // 单击提示进行搜索
            tipItems.each((index, item) => {
              $(item).mousedown(function () {
                window.open(`https://www.baidu.com/s?ie=UTF-8&wd=${$(this).text()}`);
              })
            })

            // 鼠标经过时的变化
            tipItems.each((index, item) => {
              $(item).hover(function () {
                searchTips.show();
                $(this).addClass('tipCur').siblings().removeClass('tipCur');
              }, function () {
                $(this).removeClass
                  ('tipCur').siblings().removeClass('tipCur');
              })
            })

            // 搜索框失去焦点时隐藏，获得焦点时显示
            searchInput.blur(function () {
              searchTips.hide();
            })
            searchInput.focus(function () {
              searchTips.show();
            })

          })

        })

        // 通过上下箭头来控制搜索框内容
        searchInput.keydown(function (e) {
          // 方向键下
          if (e.keyCode == 40) {
            tipIndex++;
            if (tipIndex >= tipItems.length) {
              tipIndex = 0;
            }
            tipItems.eq(tipIndex).addClass('tipCur').siblings().removeClass('tipCur');
            searchInput.val(tipItems.eq(tipIndex).text());
          }
          // 方向键上
          if (e.keyCode == 38) {
            // 要阻止方向键上光标跳到文字开头的默认行为，必须要在keydown阶段阻止
            e.preventDefault();
            tipIndex--;
            if (tipIndex <= (-1)) {
              tipIndex = (tipItems.length - 1);
            }
            tipItems.eq(tipIndex).addClass('tipCur').siblings().removeClass('tipCur');
            searchInput.val(tipItems.eq(tipIndex).text());
          }
        })
      }
    })
  </script>
```
这里主要讲一下这些功能的实现原理：

+ 使用百度进行跳转搜索
通过给百度的url传递 wd 字段的参数就可以跳转到相应的搜索结果，就像我上面写的那样。我这里通过open方法来跳转的目的是为了在新窗口中打开。但是有一点不好的是可能会被浏览器拦截，不过这是我自用的插件所以没影响。

+ 关键字提示窗口
这里实现这个提示窗口主要是用到了jsonp向相应的API发起跨域请求（如上面代码所示），然后就可以拿到相应的数据。

+ 单击提示进行搜索
这个其实就是在拿到数据并且添加进HTML中后对每条数据进行遍历，绑定一个单击事件，单击进行相应的跳转就可以了。

>大部分代码都是很好理解的，这里就不做过多的解释了

#### CSS代码：
```css
  * {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  html,
  body {
    height: 100%;
  }

  .icon {
    width: 1em;
    height: 1em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
  }

  .icon.sousuo {
    font-size: 20px;
    padding: 0 12.5px;
  }

  .main {
    width: 100%;
    background: url("./asset/images/bg.jpeg") center center / cover no-repeat;
    /* height: 700px; */
    position: relative;
    height: 100%;
  }

  .centerBox {
    position: absolute;
    top: 20%;
    left: 50%;
    transform: translateX(-50%);
  }

  .log {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    border: 1px solid #fff;
    background-color: orange;
    margin: 0 auto;
    margin-bottom: 20px;
  }

  .searchWrap {
    margin: 0 auto;
    width: 600px;
    height: 45px;
    display: flex;
    background-color: rgba(255, 255, 255, 0.8);
    align-items: center;
    border-radius: 45px;

  }

  .searchInput {
    padding-left: 2px;
    flex-grow: 1;
    height: 45px;
    background-color: rgba(0, 0, 0, 0);
    outline: none;
    border: none;
  }

  .searchTips {
    width: 98%;
    background-color: rgba(255, 255, 255, 0.9);
    margin: 0 auto;
    margin-top: 10px;
    border-radius: 4px;
    overflow: hidden;
  }

  .searchTips li {
    padding: 8px 0;
    padding-left: 10px;
  }

  .tipCur {
    background-color: rgba(0, 0, 0, 1);
  }
```

样式这里可以讲的一点就是如何使一个dom元素沾满整个屏幕：
```
  html,
  body {
    height: 100%;
  }
    .main {
    width: 100%;
    height: 100%;
  }
```
主要就是通过这些代码来实现，也非常好理解。


### 开发过程中我遇到的一些问题以及解决办法

####  **click** 和 **blur**  事件的冲突

  起初当我基本上完成大部分功能的之后进行测试，发现当点击提示框中的内容的时候竟然没有进行跳转，而是直接执行了失焦事件中的隐藏提示框的代码。我就猜想应该是由于单击的时候会使文本框失焦，并且 blur 会在 click 之前触发，进行一些尝试之后还是没有解决问题，后来百度了一下找到了解决问题的两种办法：

  1. 第一种就是通过设置定时器，通过设置``setTimeout``来使 blur 事件中的代码晚一些执行：
      ```js
              searchInput.blur(function () {
                setTimeout(function () {
                  searchTips.hide();
                }, 300)
              })
      ```
      但是这种办法的缺点也很明显，就是延迟的时间要足够长，起码要长到 click 事件的代码执行完毕，我这里测试大概最少300ms,所以在执行 blur 事件的时候能感觉到很明显的延迟，体验并不是很好。

  2. 第二种就是使用 **mousedown**事件来替代 click 事件：
      我这里采用的就是这种方式，这种方式就很好的解决了问题，但是也不是没有缺点， mousedown 事件在鼠标按下时就会触发，所以触发次数也就和按下的事件有关系，如果只是快速的按一下那么没有区别，但是按久了就会触发非常多次，所以很多场景下不适用，不过我这里没有关系，因为只要按下去了就打开新窗口不在当前window环境下了，所以没有影响。

      这里再来讲一下 **mousedown**、**mouseup**、**click**这三个事件的主要区别及应用场景：
      - click ：在同一元素上 相继触发 mousedown 和 mouseup 才会触发 click ，并且能够通过按下回车来触发。大部分场景下都是使用 click ，因为过程比较完整也不会重复触发。经测试右键不会触发。

      - mouseup ：释放鼠标时触发。右键可以触发。

      - mousedown：鼠标按下时触发。不过由于按下就触发的特性，比较的快，所以 mousedown 会在 blur 之前触发，这就是为什么使用 mousedown 可以解决冲突问题。

    这里总结一下：一次完整的鼠标单击就是这样一个过程 mousedown > mouseup > click 。 mousedown 和 mouseup 配合可以实现监听鼠标长按。具体怎么使用就看场景需求了。

#### 异步问题
当我完成了添加提示框功能之后，想要继续完成鼠标经过提示内容给其添加样式的功能，当我写完之后却发现了问题：我是直接将添加样式的代码放在了和文本框平级，我想这从逻辑上来说应该没有什么问题，但实际上却出现了问题，起初是报错提示框内容中的那个jQuery对象是空的，这就很奇怪了，我明明在每次的 input 事件执行时就给这个jQuery对象赋值了，出现这种情况也就意味着input事件发生在添加样式之后。

经过一段事件的思考我想明白了，主要是忽略了无论是 input 事件还是 Ajax 的请求，这二者都是异步的任务，而添加样式所用的过程是主线程上的同步操作，所以一定会在这两个异步任务之前生效，所以才会导致这种情况的出现，那么解决办法也就很简单了，将添加样式的代码在这两个异步任务之后执行就可以了，所以只需要将代码放进 Ajax 的回调中执行就可以了，那么这样就成功的解决了问题。同理，单击提示进行搜索的那部分代码也是一样的道理。

其后后面也有一部分代码（通过方向键控制要搜索的内容的那部分）涉及到操作提示框内容的jQuery对象，那为什么不会出现这种情况呢，到这里其实也很好理解了，因为这部分的代码也是由事件来触发的，也是异步的，并且是在最后面，所以一定是最后执行的。


关于异步单线程可以参考我的这篇文章:<a href="https://q32757468.github.io/2019/07/06/%E5%85%B3%E4%BA%8Ejs%E5%8D%95%E7%BA%BF%E7%A8%8B%E5%BC%82%E6%AD%A5%E4%B8%8E%E4%BA%8B%E4%BB%B6%E5%BE%AA%E7%8E%AF%E6%9C%BA%E5%88%B6%E7%9A%84%E5%AD%A6%E4%B9%A0/">简单理解js单线程异步与事件循环机制</a>

#### 阻止键盘方向键的默认行为
在文本框中，键盘的方向键存在着默认行为，比如说是方向键上，这会使光标移动到文本框最前面，我们在搜索的提示框中就可以通过上下键来切换搜索内容，但是同时也会触发默认行为，使光标移动到文本框最前端，这样的结果很明显不是我们想要的。

百度了一下相关的解决办法，就是阻止上箭头的默认行为，但是发现好像不起作用，最后其实发现解决办法是和解决 click 和 blur 冲突问题的办法类似，更换触发事件，将按下的**keyup**事件修改为**keydown**事件，然后这时候阻止默认行为就有效了，应该也是触发先后的问题，这样修改之后发生的另一个变化就是按键可以"连发"了,不过这也不算坏处。

### 总结
通过制作这个简单的搜索小功能还是收获挺多的，能够完全理解的话也能进步挺多的，特别是异步问题那里，之前只有其概念，现在运用起来了。
