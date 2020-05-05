---
title: php中页面分页的实现
date: 2019-06-03 21:01:46
tags:
 - PHP
categories:
 - 页面小功能
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;今天老师上课给我们讲了一下php中的翻页功能的实现，，主要是通过数据库中的limit方法实现，这一点和我预想的差不多，但是通过给页面设置id的这种办法是我没有想到的，我一开始的设想是，能不能作到不用给每个页面设置一个id，根据这个id进行跳转，而是直接设置一个变量，当进行翻页的时候这个变量的数值发生变化，然后根据这个变量来获取数据库中的内容。这个想法应该可以实现，但是可能会比较麻烦，因为在php中还是必须要刷新页面才能更新数据的，所以目前来看还是这种方法应该是最好的，那么就来看看究竟是怎么实现分页功能的。
<!-- more -->
### 代码
* 先是连接数据库：
```php
<?php
// 一个正常的连接数据库的页面
$conn=mysqli_connect('localhost','root','','test') or die('失败');
mysqli_set_charset($conn,'utf8');
$res=mysqli_query($conn,'select * from stu');
?>
```

* 实现功能的页面
```php
<body>
    <table>
    <tr>
    <td>id</td>
    <td>姓名</td>
    <td>年龄</td>
    <td>性别</td>
    </tr>
    <?php
        include 'conn.php';
        $data_count=mysqli_num_rows($res);  //获取数据表中有多少条数据
        $data_num=2; //设置一页显示多少条数据
        $id=empty($_GET['id'])?1:$_GET['id']; //判断当前页面的url栏中的id参数是否为空，如果为空则默认为1，否则则为url中id的数值
        $pg_count=ceil($data_count/$data_num);  //计算总共有多少页，ceil的用法是往上取整
        //通过limit来获取每一个页面该显示的数据
        $datas=mysqli_query($conn,"select * from stu limit ".($id-1)*2 . ','. $data_num);
        //用来排错的方法
        // if (!$datas) {
        //     printf("Error: %s\n", mysqli_error($conn));
        //     exit(); 
        //     }
        // 输出最终获取到的页面的数据
        while($row=mysqli_fetch_array($datas)){
        echo "<tr><td>$row[id]</td><td>$row[name]</td><td>$row[age]</td><td>$row[sex]</td></tr>";
        }
    ?>  
    <!-- 在href中设置id的值 -->
    <tr>
    <td><a href="?id=1">首页</a></td>
    实现上一页的功能，先判断是否是第一页，否则id-1
    <td><a href="?id=<?php echo $id==1?1:$id-1 ?>">上一页</a></td>
    实现上一页的功能，先判断是否是最后一页，否则id+1
    <td><a href="?id=<?php echo $id==$pg_count?$pg_count:$id+1 ?>">下一页</a></td>
    <td><a href="?id=<?php echo $pg_count ?>  ">尾页</a></td>
    </tr>
    </table>
</body>
```
其实代码还是很好理解的，但是自己在写的过程中还是遇到了一些问题，下面记录一下这些问题

### 我自己在编写过程中遇到的一些问题 
首先就是在我基本上已经写完代码之后一测试就爆出了一个警告，并且最终没有出现我想要的结果，于是我上网查询解决办法，于是找到了下面这一段代码：
```php
if (!$datas) {
        printf("Error: %s\n", mysqli_error($conn));
        exit(); 
        }

```

通过上述代码就没有再出现警告了，而是直接报错，提示问题在哪，我就在附近排查问题，

``
        $datas=mysqli_query($conn,"select * from stu limit ($id-1)*2 , $data_num");
``

我一开始想会不会因为是没有拼接字符串的原因,于是我修改代码改成后面是拼接字符串的形式。

``
        $datas=mysqli_query($conn,"select * from stu limit".($id-1)*2 . ','. $data_num);
``

但是一直都是出现一样的错误提示，后面经过我的测试发现是``$datas``中没有数据，但是我直接不写变量，把变量写死就能出来数据。
反复比较自己那部分的代码和老师给的代码，但是直接比对并没有发现什么不一样，后面复制老师的这行代码稍加修改结果却是正常的。
最后我终于发现了问题所在，其实我尝试的两种写法都存在问题：
* 首先是我一开始没有拼接变量，而是直接将变量写在字符串之中，我发现也是数据库在处理语句的时候出现的问题，我猜测应该是在使用变量的时候变量会把前面的空格给吞掉，导致语句执行错误。
* 后面我尝试拼接字符串的时候又出现了问题是因为我在``limit``后面少拼接了一个空格导致语句执行出错。

### 总结
写php还是不是特别的熟练，所以经常出现各种问题，所以要总结错误，加深印象，以后越写越熟练以后的错误就会越来越少
