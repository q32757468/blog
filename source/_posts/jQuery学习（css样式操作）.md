---
title: jQuery学习（css样式操作）
date: 2019-05-12 12:35:54
tags:
 - JS
 - jQuery
 - CSS
categories:
 - 学习笔记
 - jQuery学习笔记
---
### 前言
> 这次学了jQuery关于css样式的操作，通过jQuery的方法给元素添加修改样式以及改变和获取元素的各种宽高。
<!-- more -->
### 操作css样式
* css(name|pro|[,val|fn])方法 
格式1:DOM元素.css("样式名称", "值"); 
格式2:DOM元素.css({"样式名称1":"值1","样式名称2":"值2"}); 
```html
<body>
<script>
$(function(){
    var $test=$('.test');
    $test.css('height','100px').css('outline','1px solid red');
    // 这是第一种写法，添加多个css样式
    $('.test').css({
        'width':'200px',
        'font-size':'30px',
        // 这是第二种添加css样式的方法
    });
})
</script>
<div class="test">这是一个测试div</div>
</body>
```

### 操作尺寸
#### width([val|fn])方法 
```html
<body>
<script>
$(function(){
    var $test=$('.test');
    console.log($test.width());
    // 获取到数值为1520
    $test.width('200px');
    // 设置元素宽度为200px
    console.log($test.width());
    // 结果为200
})
</script>
<div class="test">这是一个测试div</div>
</body>
```
#### height([val|fn])方法
* 用法与效果与width方法一致

#### innerHeight()/innerWidth()
获取第一个匹配元素内部区域高度/宽度（包括补白、不包括边框）。

#### outerHeight/outerWidth()
* 获取第一个匹配元素外部高度（默认包括补白和边框）。
* 设置为 true 时，计算边距在内。
```html
<body>
<script>
$(function(){
    var $test=$('.test');
    console.log($test.outerHeight());
    // 21
    console.log($test.height());
    // 21
    console.log($test.outerHeight(true));
    // 填入参数true时会获取边距的数值，此时设置了外边距，值为41    
})
</script>
<div class="test">这是一个测试div</div>
</body>
```
### 操作和获取元素位置
#### offset([coordinates]) 
* 获取或设置元素相对窗口的偏移位
```html
<style>
.test{
    margin-top: 2000px;
    outline: 1px solid red;
    height: 20px;
}
</style>
<body>
<script>
$(function(){
    var $test=$('.test');
    console.log($test.offset().top);
    // 获取到值为2000
    $test.offset({top:200,left:200});
    console.log($test.offset());
    // [object Object]: {left: 200, top: 200}，位置成功发生变化
    
})
</script>
<div class="test">这是一个测试div</div>
</body>
```

#### position()
获取匹配元素相对父元素的偏移。
返回的对象包含两个整型属性：top 和 left。为精确计算结果，请在补白、边框和填充属性上使用像素单位。此方法只对可见元素有效。
```html
<style>
.test{
    /* margin-top: 2000px; */
    outline: 1px solid red;
    height: 20px;
    position: absolute;
    top: 20px;
    left: 20px;
}
.father{
    position: relative;
}
</style>
<body>
<script>
$(function(){
    var $test=$('.test');
    console.log($test.position());
    // 成功获取到了绝对定位的数值20，20  
})
</script>
<div class="father">
<div class="test">这是一个测试div</div>
</div>
</body>
```
#### scrollTop([val])与scrollLeft([val])
获取匹配元素相对滚动条顶部的偏移。
此方法对可见和隐藏元素均有效。
下面以scrollTo()为例：
```html
<style>
.test{
    margin-top: 2000px;
    outline: 1px solid red;
    height: 20px;
    position: absolute;
    top: 20px;
    left: 20px;
}
button{
    margin-top: 200px;
}
</style>
<body>
<script>
$(function(){
    console.log($('html').scrollTop()+$('body').scrollTop());
    // 当拖动滚轮，点击按钮获取数值，数值能够正确显示。
    $('button').eq(0).click(function(){
    alert(($('html').scrollTop()+$('body').scrollTop()));
    // 兼容处理，保证所有浏览器都能正确获取数值
});
    $('button').eq(1).click(function(){
    $('html').scrollTop(300)+$('body').scrollTop(300);
});
})
</script>
<div class="father">
<div class="test">这是一个测试div</div>
</div>
<button>显示scrolltop</button>
<button>设置scrolltop</button>
</body>
```


