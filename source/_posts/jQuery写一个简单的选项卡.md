---
title: jQuery写一个简单的选项卡
date: 2019-05-14 15:10:02
tags:
 - JS
 - jQuery
 - 动画
categories:
 - 页面小功能
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这次通过jQuery的方法来写一个小小的选项卡功能，通过jQuery实现起来也是十分的简单。
<!-- more -->
直接上代码：
```html
<style>
    * {
        margin: 0;
        padding: 0;
        list-style: none;
    }

    div {
        border: 1px solid #000;
        width: 800px;
        height: 400px;
        margin: 0 auto;
        position: relative;
    }

    .ch {
        top: 320px;
        left: 650px;
        position: absolute;
    }

    .ch>li {
        display: inline-block;
        width: 20px;
        height: 20px;
        background-color: #ccc;
        margin-right: 10px;
    }

    img {
        width: 100%;
        height: 400px;
        display: none;
    }

    .current {
        display: block;
        background-color: red !important;
    }
</style>

<body>
    <div>
        <ul class="imgs">
            <li><img class="current"
                    src="https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=2489492398,1961915359&fm=26&gp=0.jpg"
                    alt=""></li>
            <li><img src="https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=1287814793,457485829&fm=26&gp=0.jpg"
                    alt=""></li>
            <li><img src="https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=1501075685,574908757&fm=11&gp=0.jpg"
                    alt=""></li>
            <li><img src="https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=3662540589,314327097&fm=11&gp=0.jpg"
                    alt=""></li>
        </ul>
        <ul class="ch">
            <li class="current"></li>
            <li></li>
            <li></li>
            <li></li>
        </ul>
    </div>
    <script>
        $(function () {
            $('.ch>li').mouseenter(function () {
                $(this).addClass('current').siblings().removeClass('current');
                // 给当前操作的小方块添加样式，其他的兄弟小方块去除样式
                var index = $(this).index();
                // 获取当前是第几个小方块，用来确定是第几张图片
                $('.imgs>li>img').eq(index).addClass('current').parent().siblings().children()
                    .removeClass('current');
                    // 给对应的图片添加样式，其他的图片删除样式
            })
        })
    </script>
</body>
```
效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/zPWQ5trQbFj*6qc98*MBj*740Y0lKjmX*Ekq9jguKcY!/b/dLgAAAAAAAAA&bo=gAcSBAAAAAADB7M!&rf=viewer_4">
### 总结
* 写这个选项卡思路很简单，但是在布局的时候没有考虑好导致在给img标签删除样式的时候会比较麻烦，需要先找到父级li，然后找到他的兄弟，再找到他们的儿子，下次要注意避免出现这种情况。