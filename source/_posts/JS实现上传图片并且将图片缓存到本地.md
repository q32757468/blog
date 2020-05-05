---
title: JS实现上传图片并且将图片缓存到本地
date: 2019-09-16 09:42:06
tags:
 - JS
 - HTML
 - CSS
 - chrome插件开发
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其实这篇文章是建立在前面的<a href="https://q32757468.github.io/2019/09/15/JS%E5%AE%9E%E7%8E%B0%E4%B8%8A%E4%BC%A0%E5%9B%BE%E7%89%87%E5%B9%B6%E4%B8%94%E6%9C%AC%E5%9C%B0%E9%A2%84%E8%A7%88/">实现上传图片并且预览</a>的文章之上的，也交代了其实做这些只是为了完成我的chrome插件的开发中的自定义上传图片并且保存下来，当时一开始没有一个满意的思路，不过现在通过搜索一些资料也想到了一个我比较满意的思路，这篇博客就来将这个思路实现一下。
<!-- more -->
### 思路
我觉得最重要的就是思路，有一个满意的思路开发起来就会顺畅很多，刚开始的时候就是找不到合适的思路所以停了一段时间，现在我把我想到的思路说一下。

就是可以通过 **input:file** 来进行图片的上传，然后通过 **local storage** 来缓存上传的图片，这样就只需要上传一次，之后加载local storage中的图片就可以了。

在这之前我一直想不通的就是如何将图片保存下来，后面我看到了实现图片上传预览的这个例子我知道了原来只需要将图片转化为dataURL就可以了，还有一个问题就是该将图片保存在哪，最后觉得是保存在local storage 中是最好的。

之前我的错误思路一直想的都是如何把图片下载到本地，然后每次再加载本地的图片，首先是这样不好实现，其次是如果是这样的话只要图片在文件夹中修改或者移除，那么就会影响到页面的显示。

### 效果图
在看是如何实现之前可以先看一下实现之后的效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/kKoZ7WdcFA.JngRjpzEZEuQbzj9qZS5J9qkETV58rK0!/b/dL4AAAAAAAAA&bo=fgehAwAAAAADB*k!&rf=viewer_4">
从图片中可以看出有一个上传图标的上传框，然后下面就是上传的所有的图标，当页面关闭再重新打开也会一直存在，因为图片是从local storage中加载的，除非删除local storage中的数据，所以这样就实现了我想要的效果。

### 实现
思路讲完了，来看一下究竟是怎么样实现的：

#### HTML:
```html
  <div class="uploadBox">
    <div class="replaceBox">
      <img class="iconPreview" src="" alt="">
      +
      <input type="file" name="" id="uploadBt">
    </div>
    <input type="text" class="iconTitle" placeholder="图标标题">
    <button class="comfir">确定</button>
  </div>

  <ul class="list">
  </ul>
```
这部分就非常的简单了，就两个部分，一个是上传框，一个是图片列表。

#### CSS:
```css
 .uploadBox {
    width: 400px;
    height: 300px;
    border: 1px solid #000;
    margin: 0 auto;
    margin-top: 40px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  #uploadBt {
    border: none;
    position: absolute;
    width: 100px;
    height: 100px;
    opacity: 0;
    cursor: pointer;
  }

  .replaceBox {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100px;
    height: 100px;
    border: 2px dashed #ccc;
    margin: 0 auto;
    margin-top: 20px;
    font-size: 60px;
    font-weight: 100;
    color: rgb(107, 94, 94);
    cursor: pointer;
    box-sizing: border-box;
  }

  .iconTitle {
    border: none;
    outline: none;
    border-bottom: 1px solid #000;
    text-align: center;
    margin-top: 20px;
  }

  .iconPreview {
    /* background-color: bisque; */
    position: absolute;
    width: 100px;
    height: 100px;
  }

  .comfir {
    border: none;
    outline: none;
    background-color: cornsilk;
    border-radius: 2px;
    padding: 5px 2px;
    width: 80px;
    margin-top: 20px;
    cursor: pointer;
  }

  .list {
    margin: 50px auto;
    list-style: none;
    display: flex;
    width: 1000px;
    justify-content: center;
  }

  .list li {
    margin: 0 5px;
    background-color: aquamarine;
    border-radius: 5px;
    display: inline-flex;
    justify-content: center;
    align-items: center;
  }
```
css部分不重要，可以说的就是这个上传框样式的实现，因为css修改``input:file``的样式不是很好修改，所以我这里是通过些一个带样式的框绝对定位到``input:file``所在的区域，然后二者设置成同样的大小，将带样式的框完全覆盖住上传框，然后将上传框设置为完全透明，就实现了现在的上传框的样式。


#### JS：
最重要的就是js了
```js
$(function () {
      const iconPreview = $('.iconPreview');
      const uploadBt = $('#uploadBt');
      const iconTitle = $('.iconTitle');
      const comfir = $('.comfir');
      const list = $('.list');
      const imgList = [];
      let iconDataUrl;

      //这一部分是实现上预览的，可以参考前面的博客
      uploadBt.change(function () {
        const file = $(this)[0].files[0];
        const fr = new FileReader();
        fr.onload = function () {
          iconPreview.attr({ "src": this.result });
          iconDataUrl = this.result;
          // console.log(this.result);
        }
        fr.readAsDataURL(file);//将图片作为url读出提交给result
      })

      //单击确定按钮就可以将图片保存进local storage中
      comfir.click(function () {
        //将新添加的图标的信息添加进local storage
        // localStorage.setItem(iconTitle.val(), iconDataUrl);
        //还需要将图标的名称以及图标的dataurl都存入一个图标的数组对象当中，然后通过遍历这个数组对象来生成图标的列表。
        //可以通过一个函数来实现，函数的参数就是所需要存放的数据

        // 通过点击确认按钮调用添加图标的函数
        AddToList(iconTitle.val(), iconDataUrl);
        console.log(imgList);
      })

      //遍历local storage将其转化为一个对象数组
      function CreateList() {
        for (let i = 0; i < localStorage.length; i++) {
          let title = localStorage.key(i);
          let url = JSON.parse(localStorage.getItem(title)).url;
          let createDate = JSON.parse(localStorage.getItem(title)).createDate;
          imgList.push({ title, url, createDate });
          imgList.sort((val1, val2) => {
            return val1.createDate - val2.createDate
          })
        }
      }
      CreateList();

      //通过这个函数将每次新添加的图标添加进local storage、以及对象数组、页面中
      function AddToList(title, url) {
        imgList.push({ 'title': title, 'url': url, })
        list.append(
          `<li><img src="${url}" alt=""></li>`
        )
        const iconInfoObj = {
          url,
          'createDate': new Date().getTime()
        }
        localStorage.setItem(title, JSON.stringify(iconInfoObj));
      }

      //将对象数组中的所有对象添加到页面当中
      function AddToHtml() {
        imgList.forEach(function (item) {
          list.append(
            `<li><img src="${item.url}" alt=""></li>`
          )
        })
      }
      AddToHtml();
    })
```
需要解释的地方差不多都在注释中标出来了。

还有需要说一下的就是因为不知道local storage中对数据的排序方式是怎么样的，反正不是通过时间来排序的，所以会导致一个问题，最后在页面中生成的图标的列表会和预期的不一样（预期的是先添加的在前面），所以为了解决这个问题就需要通过时间来对生成的对象数组进行排序，就需要记录上传的时间，所以需要用对象来保存，但是在local storage中无法直接存储对象，需要进行转换，取出来的时候也同样需要进行转换，这个是需要注意的一点。

这里只实现了添加的功能，删除的功能就很简单了。

### 总结
开发我的chrome插件也是遇到了挺多的问题的，从样式效果到功能实现，不过也都解决了，也学到了很多的新东西，不错。
