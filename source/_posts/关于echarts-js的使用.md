---
title: Echarts.js的学习
date: 2019-06-08 22:03:53
tags:
 - JS
 - Echarts
categories:
 - 学习笔记
 - Echarts
---
### 前言
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最近在做期末的课程设计的时候需要做一个后台管理界面，所以需要用到一些图标插件，经过搜索我发现有好多的图标插件，最终我决定要使用echarts.js，其中一个很重要的原因就是它是有国内的百度的团队主导开发的一个项目，所以有中文文档，方便我查阅，文档查阅请看<a href="https://echarts.baidu.com">这里</a>
<!-- more -->
### 快速上手echarts.js
根据官方文档，上手使用echarts.js首先需要先引入js文件，js文件可是直接在官方网站直接下载，在引入js文件之后我们就可以开始使用了，使用之前我们要先创建一个容器，例如：
``   
 <div class="container" id="test" style="height: 400px;">
``
接着之后的就是进行初始化，
``
        var $mychart=echarts.init($('#test')[0]);
``
**注意，因为我这里是在jQuery中进行初始化的，但是自带的初始化方法必须传一个原生js对象，所以需要将jQuery对象转化为js对象**

然后就可以开始制作图标了：
```js
$(function(){
        var $mychart=echarts.init($('#test')[0]);
        var option={
            title:{
                text:'这是一个测试的图表'  //这是图表的标题
            },
            tooltip:{},
            legend:{
                data:['销量']  //这个是告诉你表格上所有的数据的类型的那种，也就是右上角的那种东西，现在先这么理解好了，到时候查了文档再详细介绍。
            },
            xAxis:{
                data:["衬衫","羊毛衫","袜子"]  //这个是x轴坐标，也就是横坐标的值
            },
            yAxis:{},
            series:[{
                name:'销量',
                type:'bar',
                data:[5,30,60] //这里是第一个系列的值
            }]
        };
        $mychart.setOption(option); //将图表的设置应用
    })
```
然后来看一下效果：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/modlggoXIDSu1a2S0dKlFgZtosSITXM1BmkHMFKwD8w!/b/dMAAAAAAAAAA&bo=gAcSBAAAAAADB7M!&rf=viewer_4">

### 再来看看一个官方文档中给出的一个南丁格尔图
我通过官方给出的在线编辑工具，一个个试了一下都是什么作用
```js
option = {
    backgroundColor: '#2c343c', //这个是设置整个图表的背景颜色的。
    visualMap: {       //通过visualmap组件来进行明暗度的映射达到一种有层次感的感觉
        show: false,  //将这个设置为true的话就会出来一个可以选择的小柱子
        min: 60,     //南丁格尔图每个区域的颜色过渡的最小值
        max: 600,   //南丁格尔图每个区域颜色过渡的最大值
        inRange: {
            colorLightness: [0, 1] //设置整个南丁格尔图的颜色的亮度
        }
    },
    series : [
        {
            name: '访问来源',
            type: 'pie',  
            radius: '55%',  //设置整个图的大小
            data:[
                {value:235, name:'视频广告'},  //通过键值对的形式来设置数值
                {value:274, name:'联盟广告'},
                {value:310, name:'邮件营销'},
                {value:335, name:'直接访问'},
                {value:400, name:'搜索引擎'}
            ],
            roseType: 'angle',  //将普通的饼图切换成南丁格尔图
            label: {
                normal: {
                    textStyle: {
                        color: 'rgba(255, 255, 255, 0.3)'  //文本颜色
                    }
                }
            },
            labelLine: {
                normal: {
                    lineStyle: {
                        color: 'rgba(255, 255, 255, 0.3)'  //线的颜色
                    }
                }
            },
            itemStyle: {
                normal: {
                    color: '#c23531',  //设置整体的颜色
                    shadowBlur: 300,  //设置阴影的模糊度
                    shadowColor: 'rgba(0, 0, 0, 0.5)'  设置阴影的颜色
                }
            }
        }
    ]
};
```
再来看一下官方的效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/aVHcBJG6xTQBxe0Wu9Myr6NsybPufWsFtuqDLz9CRO0!/b/dAQBAAAAAAAA&bo=XgQiAwAAAAADB1k!&rf=viewer_4">
可以看到这个真的很酷！

### 异步加载数据
需要实现异步加载数据其实也很简单，只要利用相关方法就可以轻松实现，比如利用jQuery中的延迟对象中的 ``done()``这个方法就可以轻松实现。
以下是官方文档中的一个异步加载数据的实例：
```js
var myChart = echarts.init(document.getElementById('main'));
// 显示标题，图例和空的坐标轴
myChart.setOption({
    title: {
        text: '异步数据加载示例'
    },
    tooltip: {},
    legend: {
        data:['销量']
    },
    xAxis: {
        data: []
    },
    yAxis: {},
    series: [{
        name: '销量',
        type: 'bar',
        data: []
    }]
});

// 异步加载数据
$.get('data.json').done(function (data) {
    // 填入数据
    myChart.setOption({
        xAxis: {
            data: data.categories
        },
        series: [{
            // 根据名字对应到相应的系列
            name: '销量',
            data: data.data
        }]
    });
});
```
以上例子就是先加载出空的图表，等待数据加载完毕就显示数据。

### 使用dataset来管理数据
我们前面的例子都是普通的数据格式，设置在各个``series``当中，在数据共享方面存在不足，使用dataset就可以解决这些问题，下面是一个简单的dataset的例子：
```js
var option={
            title:{
                text:'这是一个测试的图表'
            },
            tooltip:{},
            legend:{},
            dataset:{
                source:[
                    ['销量','销量1','销量2'],
                    ["衬衫",'5','20'],
                    ['羊毛衫','30','40'],
                    ['袜子','60','80'],
                    ['裤子','60','50']
                ]
            },
            // 声明一个 X 轴，类目轴（category）。默认情况下，类目轴对应到 dataset 第一列。
            xAxis:{
                type: 'category'
            },
            yAxis:{},
            // 声明多个 bar 系列，默认情况下，每个系列会自动对应到 dataset 的每一列。
            series:[
                {type: 'bar'},
                {type: 'bar'},
            ],
        };
```
通过以上代码也实现了一个简单的图表，但是好处在于数据可以共享，不用重复写，而且我个人更加喜欢这种方式来写数据。
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/b6Iklzz*774qdSNWl1NPw0DH2g2.T361lGsKq4nCuYo!/b/dMUAAAAAAAAA&bo=WwXhAQAAAAADB5w!&rf=viewer_4">

#### 按行还是按列映射
dataset默认是按列来进行映射，通过比较两种映射的方法，我个人还是更加习惯喜欢默认的按列进行映射,这里就不过多的对二者的差异进行比较。想要详细了解可以前去文档进行查询。

#### 维度
* 常用图表所描述的数据大部分是“二维表”结构，上述的例子中，我们都使用二维数组来容纳二维表。现在，当我们把系列（series）对应到“列”的时候，那么每一列就称为一个“维度（dimension）”，而每一行称为数据项（item）。反之，如果我们把系列（series）对应到表行，那么每一行就是“维度（dimension）”，每一列就是数据项（item）。
* 维度可以有单独的名字，便于在图表中显示。维度名（dimension name）可以在定义在 dataset 的第一行（或者第一列）。例如上面的例子中，'score'、'amount'、'product' 就是维度名。从第二行开始，才是正式的数据。dataset.source 中第一行（列）到底包含不包含维度名，ECharts 默认会自动探测。当然也可以设置 dataset.sourceHeader: true 显示声明第一行（列）就是维度，或者 dataset.sourceHeader: false 表明第一行（列）开始就直接是数据。
* 维度的定义，也可以使用单独的 dataset.dimensions 或者 series.dimensions 来定义：
第一种是在dataset中设置：
```js
var option1 = {
    dataset: {
        dimensions: [
            {name: 'score'},
            // 可以简写为 string，表示维度名。
            'amount',
            // 可以在 type 中指定维度类型。
            {name: 'product', type: 'ordinal'}
        ],
        source: [...]
    },
    ...
};
```
第二种是在series中设置：
```js
var option1 = {
    dataset: {
        dimensions: [
            {name: 'score'},
            // 可以简写为 string，表示维度名。
            'amount',
            // 可以在 type 中指定维度类型。
            {name: 'product', type: 'ordinal'}
        ],
        source: [...]
    },
    ...
};

var option2 = {
    dataset: {
        source: [...]
    },
    series: {
        type: 'line',
        // 在系列中设置的 dimensions 会更优先采纳。
        dimensions: [
            null, // 可以设置为 null 表示不想设置维度名
            'amount',
            {name: 'product', type: 'ordinal'}
        ]
    },
    ...
};
```

大部分时候都不需要手动去设置维度类型。但是如果因为数据为空之类原因导致判断不足够准确时，可以手动设置维度类型。
具体有哪些类型可以查询<a href="https://echarts.baidu.com/tutorial.html#%E4%BD%BF%E7%94%A8%20dataset%20%E7%AE%A1%E7%90%86%E6%95%B0%E6%8D%AE">文档</a>

#### 数据到图形的映射（encode）
* ``encode`` 声明的基本结构如下，其中冒号左边是坐标系、标签等特定名称，如 'x', 'y', 'tooltip' 等，冒号右边是数据中的维度名（string 格式）或者维度的序号（number 格式，从 0 开始计数），可以指定一个或多个维度（使用数组）。通常情况下，下面各种信息不需要所有的都写，按需写即可。
* 使用encode可以将数据按照需要映射到图形。
* ``encode``涉及到的知识很多，我们现在来看encode的一个简单的应用。
```js
var option2={
            // 设置dataset
            dataset:{
                // 设置好源数据，默认是以列的方式来进行映射，所以要注意好顺序
                source:[
                    // 通过一个二维数组来设置数据
                    ['物品','销量1','销量2'],
                    ["衬衫",'5','20'],
                    ['羊毛衫','30','40'],
                    ['袜子','60','80'],
                    ['裤子','60','50']
                ]
            },
            xAxis:{},
            // 这里将图表设置为横向的柱状图
            yAxis:{type:'category'},
            // 设置了两个系列
            series:[
                {
                    type:'bar',
                    // 通过encode来进行映射
                    encode:{
                        // 将销量映射到x轴
                        x:'销量1',
                        // 将物品的类型映射到y轴
                        y:'物品',
                        // 这里也可以通过索引来进行映射，就可以实现动态变更数据，但是要注意的一件事是索引是从0开始的。
                        tooltip: [1, 2]  //也可以映射tooltip，现在还不知道是干什么的
                    }
                },
                {
                    type:'bar',
                    encode:{
                        x:'销量2',
                        y:'物品',
                    }
                }
            ]
        }
```
这里还看一下效果：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/cwvwewTqRW1kd5KKMudwqewDGl5Yn5L2ioeJNOpkgfI!/b/dDYBAAAAAAAA&bo=HgXKAQAAAAADB*I!&rf=viewer_4">
* ``encode``也还有很多其他的注意事项，这里就不一一介绍了，有需要可以查询文档

#### 数据的各种格式
* 常见的图表中，保存数据的方式是通过二维表的形式来保存的，广为熟知的数据图表表格软件也都是通过二维表，这些数据也都可以转化为json格式，**网络上有很多互相转换的方法**，如果转化为csv格式的文件，那么可以使用一些 csv 工具如 dsv 或者 PapaParse 将 csv 转成 JSON。
* 在js中比较直观常见的二维格式就是二维数组了，之前前面的所有例子都是通过二维数组来存储数据的
* 除了二维数组之外比较常见的二维形式就是对象数组了，也就是键值对的形式，比如上面的例子可以这样写：
```js
source:[
    // 按行的形式
    {'物品:'衬衫','销量1':5,'销量2':20},
    {'物品:'羊毛衫','销量1':30,'销量2':40},
    {'物品:'袜子','销量1':60,'销量2':80},
    {'物品:'裤子','销量1':60,'销量2':50},
]
```
* 还有按列的形式这里就不演示了
* 个人觉得二维数组的形式写起来会更加的简单方便一些，所以我更加的喜欢二维数组的方式

#### 设置多个datase（设置多个source）
可以同时设置多组数据，然后决定使用哪一组数据：
```js
var option = {
    dataset: [{
        // 序号为 0 的 dataset。
        source: [...],
    }, {
        // 序号为 1 的 dataset。
        source: [...]
    }, {
        // 序号为 2 的 dataset。
        source: [...]
    }],
    series: [{
        // 使用序号为 2 的 dataset。
        datasetIndex: 2
    }, {
        // 使用序号为 1 的 dataset。
        datasetIndex: 1
    }]
}
```

#### 其他事项
* 根据官方文档，现在并不是所有的图表类型都支持dataset
* 官方还给了一个多个图表共享一个dataset的例子，并且有联动效果，目前还看不懂是怎么会是
* 想要了解更多可以前往官方文档进行查看

### 图表组件
echarts中有很多组件，现在就按照官方文档中的示例来先介绍一下dataZoom组件，这个组件可以将图表进行放大缩小以及拖动查看，还是很有用的，现在按照官方文档中的例子来试一试:
** 先是没有datazoom的散点图
```js
var option3={
            xAxis:{
                type:'value',
            },
            yAxis:{
                type:'value'
            },
            series:[
                {
                    name:'test',
                    type:'scatter', //设置这个为散点图
                    data: [["14.616","7.241","0.896"],["3.958","5.701","0.955"],["2.768","8.971","0.669"],["9.051","9.710","0.171"],["14.046","4.182","0.536"],["12.295","1.429","0.962"],["4.417","8.167","0.113"],["0.492","4.771","0.785"],["7.632","2.605","0.645"],["14.242","5.042","0.368"]]
                }
            ]
        }

```
* 现在加上datazoom组件
```js
dataZoom: [{ // 这个dataZoom组件，默认控制x轴。
                    type: 'slider', // 这个 dataZoom 组件是 slider 型 dataZoom 组件
                    start: 10, // 左边在 10% 的位置。
                    end: 60 // 右边在 60% 的位置。
                }],
```
来看下效果图：
<img src="http://m.qpic.cn/psb?/V131x4904WMIoW/NHVrw*69j8C.LH4Bm6taq7Q5P3ZHuAXS.dLd8hQlDgU!/b/dLgAAAAAAAAA&bo=kgUgAgAAAAADB5c!&rf=viewer_4">
**可以看出现在有了一个可以拖动的条，但是还不能进行缩放**
* 如果要可以进行缩放就需要再加上一个组件
```js
{   // 这个dataZoom组件，也控制x轴。
            type: 'inside', // 这个 dataZoom 组件是 inside 型 dataZoom 组件
            start: 10,      // 左边在 10% 的位置。
            end: 60         // 右边在 60% 的位置。
        }
```
加上以后就可以进行缩放了
**这里只以x轴为例子进行了演示，y轴也是一样的方法。

### 响应式图表
echarts中支持响应式图表，也是通过类似于媒体查询来实现的，这十分有用，因为我们的项目可能是用bootstrap来制作，那么在这种情况下响应式是必不可少的。

#### echarts组件的定位和布局
echarts中有两种定位方式：
* **left/right/top/bottom/width/height 定位方式：**
例如：``{left: 23, height: 400}``，可以不写单位，还可以写成百分比的形式，``{left: 30%, height: 40%}``
可以设置 ``left: 'center'``，表示水平居中。
可以设置 ``top: 'middle'``，表示垂直居中。

在横向，left、right、width 三个量中，只需两个量有值即可，因为任两个量可以决定组件的位置和大小，例如 left 和 right 或者 right 和 width 都可以决定组件的位置和大小。 纵向，top、bottom、height 三个量，和横向类同不赘述。

* **center / radius 定位方式：**
center
是一个数组，表示 [x, y]，其中，x、y可以是『绝对值』或者『百分比』，含义和前述相同。
radius
是一个数组，表示 [内半径, 外半径]，其中，内外半径可以是『绝对值』或者『百分比』，含义和前述相同。
在自适应容器大小时，百分比设置是很有用的。

#### 图表的方向
在echarts中有一些图表可能会很长，在pc端可以较好的显示，在移动端就不一定能够很好的显示了，所以就需要设置他们的方向：
横纵向布局的设置，一般在『组件』或者『系列』的 **orient** 或者 **layout** 配置项上，设置为 ``'horizontal'`` 或者 ``'vertical'``。

#### 实现响应式
前面有说到echarts中实现响应式是通过类似媒体查询来实现的，这就是echarts中的  **Media Query** 
在option中Media Query 的基本格式：
```js
option = {
    baseOption: { // 这里是基本的『原子option』。也就是用来设置一些通用的基本的与响应式无关的选项
        title: {...},
        legend: {...},
        series: [{...}, {...}, ...],
        ...
    },
    media: [ // 这里定义了 media query 的逐条规则。
        {
            query: {...},   // 这里写规则。
            option: {       // 这里写此规则满足下的option。
                legend: {...},
                ...
            }
        },
        {
            query: {...},   // 第二个规则。
            option: {       // 第二个规则对应的option。
                legend: {...},
                ...
            }
        },
        {                   // 这条里没有写规则，表示『默认』，
            option: {       // 即所有规则都不满足时，采纳这个option。
                legend: {...},
                ...
            }
        }
    ]
};
```

**query**的格式：
```js
{
    minWidth: 200,//设置响应式的最小宽度，表示大于200px就是这个option
    maxHeight: 300,//设置响应式的最大宽度
    minAspectRatio: 1.3//设置响应式的长宽比
}
```

**多个query被满足时的优先级**
后面的覆盖前面的

**默认 query：**
如果 media 中有某项不写 query，则表示『默认值』，即所有规则都不满足时，采纳这个option。

**容器大小实时变化时的注意事项**
如果要一直动态的改变容器的大小并且让图表的大小也随之改变，那就必须注意``query option``中出现的配置项，其他``query option``也必须出现，防止出现回不去的情况。

**关于Echarts响应式还有一件需要注意的事情**
Echarts.js中虽然有类似响应式的支持，但是这与bootstrap中的响应式有很大不同，echarts中自带的响应式不能像bootstrap中那样随着窗口大小的改变而实时的更改图表的位置以及大小，只有在网页刷新的时候或者再一次setoption的时候才会有位置或者大小的变更，虽然这也能达到响应式的目的，但是我觉得这在bootstrap中会显得十分的不协调，所以我针对这一方面进行了改进，下篇文章进行详细介绍。

### echarts中的事件和行为
echarts中自带了很多的事件和行为，我们可以通过``on``的方法来绑定。
例如下面的代码：
```js
//这是一个echarts中的单击事件，单击图表之后在控制台打印数据
myChart.on('click', function (params) {
    // 控制台打印数据的名称
    console.log(params.name);
});
```
echarts中还有很多的事件，这里就不一一介绍了，这里再说一下鼠标事件返回的参数``params``
,这个参数是一个图形的数据信息对象，格式如下：
```js
{
    // 当前点击的图形元素所属的组件名称，
    // 其值如 'series'、'markLine'、'markPoint'、'timeLine' 等。
    componentType: string,
    // 系列类型。值可能为：'line'、'bar'、'pie' 等。当 componentType 为 'series' 时有意义。
    seriesType: string,
    // 系列在传入的 option.series 中的 index。当 componentType 为 'series' 时有意义。
    seriesIndex: number,
    // 系列名称。当 componentType 为 'series' 时有意义。
    seriesName: string,
    // 数据名，类目名
    name: string,
    // 数据在传入的 data 数组中的 index
    dataIndex: number,
    // 传入的原始数据项
    data: Object,
    // sankey、graph 等图表同时含有 nodeData 和 edgeData 两种 data，
    // dataType 的值会是 'node' 或者 'edge'，表示当前点击在 node 还是 edge 上。
    // 其他大部分图表中只有一种 data，dataType 无意义。
    dataType: string,
    // 传入的数据值
    value: number|Array
    // 数据图形的颜色。当 componentType 为 'series' 时有意义。
    color: string
}
```
**echarts中事件与行为更为详细的信息可以查阅相关的<a href="https://echarts.baidu.com/tutorial.html#ECharts%20%E4%B8%AD%E7%9A%84%E4%BA%8B%E4%BB%B6%E5%92%8C%E8%A1%8C%E4%B8%BA">文档</a>，这里就不详细说明了

### 总结
到这里就介绍了echarts中的一些基本的概念以及信息，还不是特别全面，算是稍微认识了一下echarts，echarts的功能十分的强大可自定义的程度也是非常的高，官方文档有非常详细的配置项介绍，如果感兴趣的话可以慢慢去学习。