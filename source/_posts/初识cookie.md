---
title: cookie的学习以及使用
date: 2019-05-21 15:24:59
tags:
 - JS
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这篇博客经过不断更新就成了现在的版本，最开始学习cookie的时候并没有用cookie具体来实现什么功能，虽然那个时候有思路，但并没有做出来，而经过这么长的时间我已经通过cookie来实现了一个简单的登录功能以及掌握了cookie的更多使用方式，来记录一下。
<!-- more -->
### 什么是cookie？
这是百度百科上的一段话：Cookie，有时也用其复数形式 Cookies，指某些网站为了辨别用户身份、进行 session 跟踪而储存在用户本地终端上的数据（通常经过加密）。定义于 RFC2109 和 2965 中的都已废弃，最新取代的规范是 RFC6265 [1]  。（可以叫做浏览器缓存）

我们就可以了解到cookie的作用，cookie就是存储了一些信息在本地，并且有一定的存在时间，cookie有这样的功能，就能给用户带来很多遍历，包括不用重复登录之类的。

### cookie的一些特点
* cookie的生命周期：
·默认情况下是一次会话，也就是直到浏览器被关闭。
·如果通过expires设置了时间，并且时间还没有过期，那么关闭浏览器后下次再打开浏览器，cookie还是存在的。
·如果通过expires设置了时间，但是过期了，那么就会立即删除这个cookie，所以要删除一个cookie，只要让他过期就好了。


* cookie数据的格式
·cookie默认是不会保存任何数据的。
``document.cookie="name=hsl"``可以通过这种方式来添加cookie
·在设置cookie时只能一条一条的设置，不能一条命令设置多条cookie。
cookie有大小和个数的限制：
个数：20~50
大小：4kb左右

* cookie的作用范围
·不同浏览器之间的cookie不能共用
·同一浏览器中，存放cookie的路径的子目录可以访问保存的cookie，但是父目录不能。
``document.cookie="name=hsl;path=/;"``但是可以通过加上path等于根目录使网站下的所有目录都能访问cookie。
·默认情况下二级域名之间的cookie不能相互访问，但是设置了主域名之后就可以，``domain=hslweb.net``。

### 封装cookie的一些方法
如果不封装方法的，直接获取cookie中我们想要的值还是有一些麻烦的，还有删除一条cookie也是一样，所以这里学习别人封装的两个方法来帮助我们简化操作。
+ 创建一条cookie
```js


```
虽然本身创建一条cookie使很简单的，但是封装一下能够使调用起来更加的方便明了。

+ 获取我们想要的cookie
```js
function getCookie(name) {
                var strcookie = document.cookie; //获取cookie字符串
                var arrcookie = strcookie.split("; "); //分割
                //遍历匹配
                for (var i = 0; i < arrcookie.length; i++) {
                    var arr = arrcookie[i].split("=");
                    if (arr[0] == name) {
                        return arr[1];
                    }
                }
                return "";
            }
```
通过以上封装，我们只需要调用函数，然后传递我们想要获取的cookie作为参数就可以获取到我们想要的cookie的值了。

+ 删除一条cookie
```js
function deleteCookie(name) {
                var date = new Date();
                date.setTime(date.getTime() - 10000);
                document.cookie = name + "=name; expires=" + date.toGMTString();
            }
```
前面也说过要删除一条cookie只需要让它过期就可以了这个函数就实现了这样的功能，用起来也方便。


### 创建条简单的cookie
```js
<script>
    var data=new Date();
    data.setDate(data.getDate()+1);//设置时间
    document.cookie="name=hsl;path=/;expires="+data.toGMTString()+";";//创建一个cookie，设置了路径以及过期的时间为后一天。
</script>
```

### 通过cookie来实现登录注销功能
先来简单的说一下思路，思路其实很简单，只需要当我们通过Ajax发送请求登录成功之后将我们获取到的用户名（如果登录是通过用户名来登录可以直接在前端获取到，否则可以由后端传过用户名来）保存到cookie中，可以给cookie设置一个期限，那么在这个期限内就都可以不用重复登录了，也可以不设置，关闭浏览器后就得重新登录，然后如果cookie中有用户名就只要把原本是登录注册的地方替换为用户名以及注销，没有用户名则依然是登陆注册，如果要注销则将cookie删除就可以了。

那么下面就是一个简单的demo：
```html
                    <li class="nav-item ml-4">
                        <a class="nav-link" href="./register.html" id="register_link" aria-disabled="true">注册</a>
                    </li>

                    <li class="nav-item pt-1 ml-4">
                        <a class=" btn btn-outline-primary btn-sm" id="logon_link" role="button" href="./logon.html">登录
                            <span class="sr-only">(current)</span></a>
                    </li>
                    <li class="nav-item ml-4">
                        <a class="nav-link" href="#" id="user_name" aria-disabled="true" style="display: none">admin</a>
                    </li>
                    <li class="nav-item ml-4">
                        <a class="nav-link" href="./index.html" id="logout_btn" aria-disabled="true"
                            style="display: none">注销</a>
                    </li>
```
用户名和注销是默认不显示的，登录成功后则将他们显示，将登录注册设置为不显示。

```js
    <script>
        $(function () {
            // 获取cookie中的值
            function getCookie(name) {
                var strcookie = document.cookie; //获取cookie字符串
                var arrcookie = strcookie.split("; "); //分割
                //遍历匹配
                for (var i = 0; i < arrcookie.length; i++) {
                    var arr = arrcookie[i].split("=");
                    if (arr[0] == name) {
                        return arr[1];
                    }
                }
                return "";
            }
            getCookie('name');

            // 删除cookie的方法
            function deleteCookie(name) {
                var date = new Date();
                date.setTime(date.getTime() - 10000);
                document.cookie = name + "=name; expires=" + date.toGMTString();
            }
            (function () {
                // 获取以后要操作的元素
                var register_link = $('#register_link');
                var logon_link = $('#logon_link');
                var user_name = $("#user_name");
                var logout_btn = $('#logout_btn');
                var name = getCookie('name');

                // 点击注销删除cookie
                logout_btn.click(function () {
                    deleteCookie('name');
                })

                // 判断cookie中是否有存放用户名
                if (name) {
                    // 如果有，则将登录注册隐藏，替换成用户名和注销
                    console.log('这里面有东西');
                    user_name.text(name);
                    register_link.hide();
                    logon_link.hide();
                    user_name.show();
                    logout_btn.show();
                } else {
                    // 如果没有，则将用户名和注销隐藏
                    console.log('这里面是空的');
                    register_link.show();
                    logon_link.show();
                    user_name.hide();
                    logout_btn.hide();
                }
            })();
        })
    </script>
```
>这里只写了前端的逻辑，后端php的代码在这里就不介绍了，其实就是对用户名和密码进行一个匹配，成功了则返回相应的数据

### 总结：
cookie的创建与设置还是很简单的，但是cookie的作用却很强大，要好好使用。cookie应用做多的场景应该就是登录功能的实现了，必须要掌握。
