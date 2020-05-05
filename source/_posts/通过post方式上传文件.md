---
title: 通过post方式上传文件
date: 2019-05-21 10:21:09
tags:
 - PHP
categories:
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这次学习一下通过表单得到post方式来向后端上传文件，以及使用表单来上传文件的注意点及注意事项。
<!-- more -->
先上代码：
```html
<body>
    <form action="post上传文件.php" method="POST" enctype="multipart/form-data">
        <input type="file" name="upFile" id=""><br>
        <input type="submit" name="tj">
    </form>
</body>
```
* 这里之所以使用post方式上传而不使用get方式上传是因为get方式上传有文件大小方面的限制，最多2000个字符，所以使用post
* 很正常的一个表单，设置了控件类型以及与后端交互时要用到的name
* 但是要注意的是上传文件时除了要设置表单类型为 ``file``之外，还需要给form添加一个``enctype``属性，并且把它的值设为``multipart/form-data``，只有这样才能正常上传。

我们再来看看php部分的代码：
```php
<?php
print_r($_FILES);
echo '<br>';
$fileInfo=$_FILES['upFile'];
print_r($fileInfo);
?>
```
这个时候我们就不能再使用post方式来获取文件的内容了，我们需要用file方式。
我么再来看看打印的结果：
```
Array ( [tj] => 提交 ) //这个时候post变量获取到的东西就不是我们想要的东西了。
Array ( [upFile] => Array ( [name] => 后面问题.png [type] => image/png [tmp_name] => E:\xampp\tmp\phpDC76.tmp [error] => 0 [size] => 32458 ) )  //而通过file变量获取到的数组里面就有我们想要的东西
Array ( [name] => 后面问题.png [type] => image/png [tmp_name] => E:\xampp\tmp\phpDC76.tmp [error] => 0 [size] => 32458 )
//然后再从数组中取出我们想要的文件，就可以得到文件的信息。
```
* 但是其实这个时候也不是我们想要的结果，因为我们会发现 ``E:\xampp\tmp\``这个目录下是空的，并没有我们上传的文件
* 这是因为这其实是一个临时文件夹，如果我们需要永久保存我们想要的内容我们需要将文件保存到我们自己建立的一个文件夹中。
* 这个时候我们就需要使用到这个函数``move_uploaded_file(file,newloc)``
第一个参数规定文件的原路径，第二个参数规定文件的新路径已经文件名。

下面将代码改进一下：
加上一行代码：
```php
move_uploaded_file($fileInfo['tmp_name'],"./test_imgs/".$fileInfo['name']);
```
这个时候我们再看设置的那个新路径，就已经成功将文件移动过来了。
### 总结：
上传文件的表单与其他表单有些许不一样，要多注意，获取内容的方法也不太一样，并且要注意``move_uploaded_file``函数的使用，第二个参数写相对路径的时候，如果是相对与当前路径开始的话一定不能遗漏了``./``来表示当前路径，比如我刚开始这样写就报错：``"test_imgs/"``,而这样写就是对的:``"./test_imgs/"``所以以后要多注意。