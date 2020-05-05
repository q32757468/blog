---
title: jQuery+php实现一个购物车的功能
date: 2019-06-30 18:23:06
tags:
 - JS
 - PHP
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在前面学习jQuery的时候，就学习过ajax如何与后端进行交互，虽然学习过php，但是平时使用的比较少，练的也比较少，Ajax也是，虽然实现动态网站上的各种功能的思路都比较清楚，但是写起来还是比较费劲啊，动不动就报错，特别是这次由于比赛需要，需要在一个静态页面的基础上实现动态的添加购物车的功能，就进行了一次尝试着写，按照我自己的思路，最终经过比较长的时间完成了这一功能，特此来记录一下。
<!-- more -->
### 思路
先来说说实现的思路：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其实我觉得思路是非常简单的，无非就是在前端通过添加购物车的按钮发送Ajax请求，然后将需要添加到购物车的商品的信息提交到后台，然后后台再拿到前端发过来的数据，将数据存放到数据表中，然后前端再在购物车页面通过Ajax请求后台，后台将所有数据表中的数据返回前端，然后前端将获取到的数据拼接到页面中，虽然原理非常的简单，但是实现的过程还是不容易啊，不过为了比赛，还是需要更加的熟练。

### 这过程中遇到的问题
+ 第一个问题就是对MySQL的语句不是很熟练，每次写语句的时候都是投机取巧的写，并不是完全手打，而且MySQL的很多命令都忘了，即使没忘的也不是很熟
+ 第二个问题就是在前端发送请求之前需要拿到一些元素，需要使用到jQuery中的很多筛选元素的方法，这里之前用的也比较少，需要先去查文档才会用，比如像``parents()``之类的。
+ 第三个就是php方面对语句不熟。

### 最终实现功能的主要代码
其中很重要的一个也是我想了很久的就是后端究竟要返回什么样的数据，前端又该如何拿到数据。
+ PHP部分：
```php
<?php
include 'conn.php';
// 通过加这条header语句就可以配合后面的函数将输出的结果转化为json格式
header('Content-Type:application/json; charset=utf-8');
$res = mysqli_query($conn, 'select * from cart');
// 将数据先放到一个数组中
$data = array();
while ($row = mysqli_fetch_array($res)) {
    // 每次循环都将结果存放到这个数组中，由于这里是一个二维表，所以也要用二维数组来存放，一开始没有想明白怎么创建一个键值对的二维数组，折腾了一会就明白了
    $data[] = ['name' => "$row[name]", 'quantity' => "$row[quantity]", 'price' => "$row[price]",];
}
// 然后将数组转化为json格式，这样返回给前端的数据就是一个对象数组了，前端就可以通过jQuery中的``$.each()``方法来对数组进行遍历，然后拿到想要的东西
echo json_encode($data);
```

+ 前端部分
```js
(function () {
        var cart_item = $('.cart_item');
        $.ajax({
            type: 'get',
            url: './php/cart_item.php',
            success: function (msg) {
                // 通过循环就可以拿到所有的数据，并且将每条数据都拼接到页面中去
                $.each(msg, function (index, obj) {
                    // 这里进行拼接，说实话我觉得拼接真是一件麻烦的事情，不知道有没有更简单的方法
                    var item = '<tr class="t_body"><td><input type="checkbox"></td><td> <img src="' + obj.name + '" alt=""></td><td>shoes</td><td class="item_pri">$2400</td><td class="item_num"><input value="1" type="number"></td><td class="item_total">2222.00$</td></tr>';
                    $('tbody').prepend(item);
                })
            },
            error: function (xhr) {
                console.log(xhr);
            }
        })
    })();
```

### 总结
感觉上面说了一堆废话，其实就是对Ajax和php还不是很熟练所导致的，写的不是很顺，以后多写应该会有所改观。不过通过这几次的写交互页面，也学到了很多，比如如何在php中返回一个对象（通过数组，然后将数组转化为json），以及前端如何较好的处理后端的数据，还有就是后端的一些逻辑处理也想明白了。以后要多回来看前面写过的代码，
