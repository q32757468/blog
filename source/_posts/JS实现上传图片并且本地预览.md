---
title: JS实现上传图片并且本地预览
date: 2019-09-15 15:12:15
tags:
 - JS
 - HTML
 - CSS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前面开发chrome插件还是遇到了挺多的困难的，其中一个就是如何将上传的图片缓存到本地，思路很简单，但是一直不知道该如何实现，现在有了办法了，主要是想通过上传图片然后通过img标签将其显示出来，再通过 localStorage 对其进行储存，这样是一个比较好的方案，我这篇文章所写的图片的预览也就是对第一步的一个实现。
<!-- more -->
### 实现思路
其实思路还是比较简单的，我们都知道 input file 有一个 files 对象，这个对象中就保存了上传的文件的信息，我们只需要将上传的文件通过一些方法转化为 url ，然后通过 img 标签的 src 来加载它，就可以实现我们想要的功能了，这里主要通过两种不依赖后端的方式来实现。

### 使用 FileReader
**FileReader**也是 html5 的一个新的 API  ，可以通过它来处理文件：

HTML：
```html
  <input type="file" class="uploadFile" name="test" id="">
  <img src="" alt="" class="preview">
```
这里只做演示，所以代码很简单，主要就是一个上传文件的input，然后一个用来显示图片的 img 标签。

JS：
```js
    $(function () {
      const uploadFile = $('.uploadFile');
      const preview = $('.preview');
      uploadFile.change(function () {
        let file = $(this)[0].files[0];
        let fr = new FileReader();
        fr.onload = function () {
          // 通过result属性获取到转化后的url
          preview.attr({ "src": this.result });
        }
        fr.readAsDataURL(file);//将图片作为url读出提交给result
      })
    })
```

### 使用 window.URL
html部分的代码是一样的。

JS：
```js
    $(function () {
      const uploadFile = $('.uploadFile');
      const preview = $('.preview');
      uploadFile.change(function () {
        let file = $(this)[0].files[0];
        //通过url对象来将图片转化为url
        let URL = window.URL || window.webkitURL;
        let imgURL = URL.createObjectURL(file);
        preview.attr({ "src": imgURL });
      })
    })
```

最后可以来看一下实现之后的效果：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/xbxICjHBzOXFUs7lSMdodJbW6dD*wcK7vkOuWjf7wKQ!/b/dLgAAAAAAAAA&bo=WgMoAQAAAAADB1I!&rf=viewer_4">

### 今天新学的一点小知识
今天这里又学到一些新东西，就是event.target,这个东西和this有点像，但是又有区别，target指向的dom对象永远不会变化，而this的指向可能会因为冒泡之类的而改变.



### 总结
其实总的来说不管是使用那种方式来实现预览图片其原理都是一样的。

区别就在于 转化为 url 时所用的方法不一样。

还有就是兼容性问题，其实都差不多，老的ie一样还是都不兼容，可能用 **FileReader** 会好一点。
