---
title: jQuery动画做一个折叠菜单
date: 2019-05-14 17:48:52
tags:
 - JS
 - jQuery
 - 动画
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前面利用了jQuery动画做了一个对联广告，这次再次利用jQuery的其他动画做一个折叠菜单，做出一个较为美观的折叠菜单。
<!-- more -->
直接上代码：
* css部分
```css
<style>
    * {
        margin: 0;
        padding: 0;
        list-style: none;
    }
    .l1{
        width: 400px;
        margin: 0 auto;
        margin-top: 10px;
    }
    .l1>li{
        /* border: 1px solid #000;
        border-collapse: collapse!important; */
        outline: 1px solid black;
        font-size: 20px;
        /* height: 40px;
        line-height: 40px; */
    }
    .l1>li>div{
        outline: 1px solid black;
        border-top: 1px solid #000;
        position: relative;
    }
    .l1>li>div>span{
        display: inline-block;
        position: absolute;
        right: 10px;
    }
    .l2{
        display: none;   
    }
    .l2>li{
        /* outline: 1px solid black; */
        /* border: 1px solid #000;  */
        border-collapse: collapse!important; 
        font-size: 16px;
        height: 40px;
        line-height: 40px;
    }
    .trans{
        transform: rotate(90deg);
        -ms-transform: rotate(90deg);		/* IE 9 */
        -webkit-transform: rotate(90deg);	/* Safari and Chrome */
        -o-transform: rotate(90deg);		/* Opera */
        -moz-transform: rotate(90deg);		/* Firefox */
    }
</style>
```
* html部分
```html
<body>
    <ul class="l1">
        <li>
            <div>一级菜单<span>></span></div>
            <ul class="l2">
                <li>二级菜单</li>
                <li>二级菜单</li>
                <li>二级菜单</li>
                <li>二级菜单</li>
            </ul>
        </li>
        <li>
            <div>一级菜单<span>></span></div>            
            <ul class="l2">
                <li>二级菜单</li>
                <li>二级菜单</li>
                <li>二级菜单</li>
                <li>二级菜单</li>
            </ul>
        </li>
        <li>
            <div>一级菜单<span>></span></div>
            <ul class="l2">
                <li>二级菜单</li>
                <li>二级菜单</li>
                <li>二级菜单</li>
                <li>二级菜单</li>
            </ul>
        </li>
        <li>
            <div>一级菜单<span>></span></div>
            <ul class="l2">
                <li>二级菜单</li>
                <li>二级菜单</li>
                <li>二级菜单</li>
                <li>二级菜单</li>
            </ul>
        </li>
    </ul>
</body>
```
* js部分
```javascript
<script>
    var index=1;
    $(function(){
        $('.l1>li>div').click(function(){
            index++;
            // 因为动画队列的存在，所以在执行新的动画之前要停止之前的动画
            $(this).next().stop().slideToggle();
            if(index%2==0){
                $(this).children().addClass('trans');
            }
            else{
                $(this).children().removeClass('trans');
            }
        })
    })   
    </script>
```
效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/7JamsbEg12boHIBr8beMA0qQTVKEhSMWwwhtSmpNkEA!/b/dAcBAAAAAAAA&bo=gAcSBAAAAAADB7M!&rf=viewer_4">

### 总结
* 要注意要找对执行动画的元素，可以用next(),chidren()等方法来找。
* 要注意动画队列，当我们快速多次的触发动画的时候，每触发一次就会加入动画队列，并且按照顺序会全部执行完毕，如果在执行新的动画之前我们不先停止之前的动画，那么就不会立即执行我们想要的动画，并且在我们不触发动画之后很有可能还会有很长一段的动画要执行。