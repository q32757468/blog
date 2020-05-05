---
title: 关于classList的使用
date: 2019-05-10 10:32:13
tags:
 - JS
categories:
---
# 前言
> 关于classList的学习总结，作为DOMTokenList对象该属性用于在元素中添加、移出、切换css类，使用classList可以实现十分多的功能，十分的有用，以下是我的学习笔记。
<!-- more -->


语法：elem.classList
方法：
add( String[,String])：添加指定的类值。如果这些类已经存在于元素的属性中，那么他们将被忽略
remove(String[,String])：删除指定的类值item(Number)：按集合中的索引返回类值
toggle(String[,force])：
* 当只有一个参数的时，切换class value ，即如果类存在，删除它，并返回false，如果类不存在，则添加它并返回true。
* 当存在第二个参数时，如果第二个参数的计算结果为true，则添加指定的类值，如果计算结果为false，则删除它

contains(string)：检查元素的类属性中是否存在指定的类值
属性：
length：返回类列表中类的数量，该属性是只读的

下面是利用classList实现标签栏切换的一个小例子：
```html
<style>
    div {
       width: 500px;
        margin: 10px;        
        
    }
    .db{
        height: 50px;
        width: 300px;
        margin: 0 auto;
        border: none;
    }
    .dh{
        background: #666;
        height: 25px;
        width: 300px;
        margin: 0 auto;
        color: white;
        text-align: center;
        border: none;
    }
    .dhd{
        margin: 0 0px;
        width: 100px;
        float: left;   
        margin-top: 3px; 
    }
    .dbd{
        width: 100px;
        height: 50px;
        float: left;
        margin: 0 0px;
        display: none;
        width: 300px;
    }
    .current{
        display: block;
        background: white;
        color: black;

    }
    .ttt{
        border: 1px solid black;
        width: 300px;
    }
</style>

<body>
    <div class="ttt">
        <div class='dh'>
            <div class="dhd current">标签栏一</div>
            <div class="dhd">标签栏二</div>
            <div class="dhd">标签栏三</div>
        </div>
        <div class='db'>
            <div class="dbd current">这是内容一</div>
            <div class="dbd">这是内容二</div>
            <div class="dbd">这是内容三</div>
        </div>
    </div>
    <script>
        // 利用classList的方法给当前元素添加样式让其显示，其他元素默认不显示，以达到切换效果
        var hs=document.getElementsByClassName('dhd');
        var ds=document.getElementsByClassName('dbd');
        for(var i=0;i<hs.length;i++){
            hs[i].onmouseover=function(){
                for(var i=0;i<ds.length;i++){
                    if(hs[i]==this){
                        ds[i].classList.add('current');
                        hs[i].classList.add('current');
                    }
                    else{
                        ds[i].classList.remove('current');
                        hs[i].classList.remove('current');
                    }
                }
            }
        }
    </script>
</body>
```
