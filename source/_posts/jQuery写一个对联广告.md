---
title: jQuery动画之写一个对联广告
date: 2019-05-14 16:28:25
tags:
 - JS
 - jQuery
 - 动画
categories:
 - 页面小功能
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;jQuery自带了许多动画效果，我们可以利用这些动画效果比较方便的做出一个对联广告。
<!-- more -->
直接上代码：
```html
<style>
    body {
        margin: 0;
        padding: 0;
        height: 2000px;
    }

    .left,
    .right {
        width: 80px;
        height: 200px;
        background: red;
        position: fixed;
        top: 260px;
    }

    .right {
        right: 0px;
    }
</style>

<body>
    <div class="left">
    </div>
    <div class="right">
    </div>
    <script>
        $(function () {
            $('.left,.right').hide();
            // 在网页往下滚动之前先隐藏
            var $height = $('body,html').scrollTop();
            $(window).scroll(function () {
                // 这里用到了scroll事件来监听网页的滚动
                // 通过scrollTop方法来获取滚动的值
                $height = $('body,html').scrollTop();
                // 如果大于500就通过动画显示出来，否则隐藏
                if ($height >= 500) {
                    $('.left,.right').show(1000);
                } else {
                    $('.left,.right').hide(1000);
                }
            })
        })
    </script>
</body>
```
### 总结：
* 这里一定要用到scroll事件，否则无法动态获取到滚动了多少
* 在使用scrollTop方法获取网页的滚动的值时一定要这样写`$('body,html').scrollTop();`既要写body，也要写html，兼容处理，如果只写一个可能会获取到不正确的值
