# 一、概述
百度echarts就是一个数据可视化的工具库,跟jQuery类似的使用方法。可以下载文件,可以cdn引入,可以npm安装。

**注意**：这里学习的是手动引入的 echarts5.4 版本。

# 二、基本使用和概念
## 2.1 基本使用
无论是那种引入方式，echarts的使用步骤主要是四步。如下：

- 步骤一：下载并引入Echarts；本地下载、cdn引入、npm安装常见的三种方式。

- 步骤二：准备一个具备大小的DOM容器,即定义一个有明确宽高的div作为图表渲染的容器。

- 步骤三：初始化Echarts实例对象；获取准备好的dom元素，通过echarts.init() 方法初始化一个echarts实例

- 步骤四：利用实例的setOption()方法绘制图表,它接收一个配置对象。这个配置对象指定了要绘制图表的配置项和数据。

**注意**：一般我们会把配置对象声明抽离出来定义一个 options 变量接收，然后再把这个变量传递给echarts实例方法 setOption(options)。 options是一个 json 数据格式的配置写法也就是键值对的形式。如此就可以绘制一个简单的图表了。

而我们学习的就是各种图表的配置项,不会就看文档或百度即可。

## 2.2 常见概念
在学习具体配置项之前先熟悉熟悉一些常见的概念。

1. 图表容器：

用来装绘制图表的div元素，调用echarts.init()方法初始化echarts实例时需要传入该节点对象。

**注意**：一定要声明宽高不然渲染不出来，当然这个宽高可以是响应式的依赖于父元素。

当容器大小改变时可以通过监听页面的 window.onresize 事件获取浏览器大小改变的事件，然后调用echarts实例的resize()方法重新绘制图表以实现同步改变图表的大小。


```
<!-- 容器改变时同步改变图表大小 -->
window.addEventListener('resize', function() {
  myChart.resize();
})
```

2. 样式：

用来改变绘制图表内图形元素或者文字的颜色、明暗、大小、宽高等等的配置选项。常用的配置是调色盘和直接样式配置项两种。不记得配置项时查看官方手册即可。

调色盘：可以认为就是一个字符串颜色值数组，它给定了一组颜色，图形、系列会自动从其中选择颜色。 优先级从内往外。
可以option最外层设置全局的调色盘，也可以在series配置选项里设置系列自己专属的调色盘。

直接的样式设置项：是比较常用设置方式。纵观 ECharts 的 option 中，很多地方可以设置 itemStyle、lineStyle、areaStyle、label 等等。这些的地方可以直接设置要绘制图形元素的颜色、线宽、点的大小、标签的文字、标签的样式等等。

高亮的样式配置项：emphasis

在鼠标悬浮到图形元素上时，一般会出现高亮的样式。默认情况下，高亮的样式是根据普通样式自动生成的。但是高亮的样式也可以自己定义，主要是通过 emphasis 属性来定制。

之所以提这个是因为ECharts4之后就推荐使用normal的配置方法了。

```
<!-- ECharts4 之后的写法 -->
option = {
  series: {
    type: 'scatter',

    // 普通样式。
    itemStyle: {
      // 点的颜色。
      color: 'red'
    },
    label: {
      show: true,
      // 标签的文字。
      formatter: 'This is a normal label.'
    },

    // 高亮样式。
    emphasis: {
      itemStyle: {
        // 高亮时点的颜色。
        color: 'blue'
      },
      label: {
        show: true,
        // 高亮时标签的文字。
        formatter: 'This is a emphasis label.'
      }
    }
  }
}
```

```
<!-- 在 ECharts4 以前，高亮和普通样式的写法 -->
option = {
  series: {
    type: 'scatter',

    itemStyle: {
      // 普通样式。
      normal: {
        // 点的颜色。
        color: 'red'
      },
      // 高亮样式。
      emphasis: {
        // 高亮时点的颜色。
        color: 'blue'
      }
    },

    label: {
      // 普通样式。
      normal: {
        show: true,
        // 标签的文字。
        formatter: 'This is a normal label.'
      },
      // 高亮样式。
      emphasis: {
        show: true,
        // 高亮时标签的文字。
        formatter: 'This is a emphasis label.'
      }
    }
  }
}
```


3. 数据集：

按照官方的说法，这个配置项使用 dataset 选项标识，是从 ECharts4 开始支持使用，是专门用来管理绘制图表所需数据的配置项，所以更推荐使用数据集来管理数据。虽然每个系列都可以在 series.data 中设置数据，个人现在开发也是在每个系列的data选项中定义数据。但是如果可以还是推荐使用数据集。


- 在系列中设置数据
```
option = {
  xAxis: {
    type: 'category',
    data: ['Matcha Latte', 'Milk Tea', 'Cheese Cocoa', 'Walnut Brownie']
  },
  yAxis: {},
  series: [
    {
      type: 'bar',
      name: '2015',
      data: [89.3, 92.1, 94.4, 85.4]
    },
    {
      type: 'bar',
      name: '2016',
      data: [95.8, 89.4, 91.2, 76.9]
    },
    {
      type: 'bar',
      name: '2017',
      data: [97.7, 83.1, 92.5, 78.1]
    }
  ]
};
```
这种方式的优点是，适于对一些特殊的数据结构（如“树”、“图”、超大数据）进行一定的数据类型定制。 但是缺点是，常需要用户先处理数据，把数据分割设置到各个系列（和类目轴）中。此外，不利于多个系列共享一份数据，也不利于基于原始数据进行图表类型、系列的映射安排。

- 在数据集中设置数据
```
option = {
  legend: {},
  tooltip: {},
  dataset: {
    // 提供一份数据。
    source: [
      ['product', '2015', '2016', '2017'],
      ['Matcha Latte', 43.3, 85.8, 93.7],
      ['Milk Tea', 83.1, 73.4, 55.1],
      ['Cheese Cocoa', 86.4, 65.2, 82.5],
      ['Walnut Brownie', 72.4, 53.9, 39.1]
    ]
  },
  // 声明一个 X 轴，类目轴（category）。默认情况下，类目轴对应到 dataset 第一列。
  xAxis: { type: 'category' },
  // 声明一个 Y 轴，数值轴。
  yAxis: {},
  // 声明多个 bar 系列，默认情况下，每个系列会自动对应到 dataset 的每一列。
  series: [{ type: 'bar' }, { type: 'bar' }, { type: 'bar' }]
};
```

数据设置在 数据集（dataset） 中，会有这些好处：

- 能够贴近数据可视化常见思维方式：（I）提供数据，（II）指定数据到视觉的映射，从而形成图表。

- 数据和其他配置可以被分离开来。数据常变，其他配置常不变。分开易于分别管理。

- 数据可以被多个系列或者组件复用，对于大数据量的场景，不必为每个系列创建一份数据。

- 支持更多的数据的常用格式，例如二维数组、对象数组等，一定程度上避免使用者为了数据格式而进行转换。

先观望。


4. 坐标轴：

直角坐标系中的 x/y 轴，在echarts中分别使用xAxis、yAxis两个配置项配置。

它们都由轴线、刻度、刻度上的标签、轴标题四个部分组成。普通的二维数据坐标系都有 x 轴和 y 轴，通常情况下，x 轴显示在图表的底部即横向，y 轴显示在左侧即竖向。

x 轴常用来标示数据的维度，这个维度一般用来指数据的类别，是观察数据的角度，例如“销售时间” “销售地点” “产品名称”等。

y 轴常常用来标示数据的数值，数值是用来具体考察某一类数据的数量值，也是我们需要分析的指标，例如“销售数量”和“销售金额”等。

轴线：echarts使用axisLine，可以设置轴线两端的箭头，轴线的样式等。

刻度：echarts使用axisTick，可以设置轴线上刻度线的长度，样式等。

刻度标签：echarts使用axisLabel，可以设置刻度线上标签的文字对齐方式，自定义刻度标签内容等。


- 坐标轴常见配置
```
option = {
  xAxis: {
    <!-- 轴线配置 -->
    axisLine: {
      symbol: 'arrow',
      lineStyle: {
        type: 'dashed'
        // ...
      }
    },
    <!-- 刻度配置 -->
    axisTick: {
      length: 6,
      lineStyle: {
        type: 'dashed'
        // ...
      }
    },
    <!-- 刻度标签配置 -->
    axisLabel: {
      formatter: '{value} kg',
      align: 'center'
      // ...
    }
  },
  yAxis: {
    axisLine: {
      symbol: 'arrow',
      lineStyle: {
        type: 'dashed'
        // ...
      }
    },
    axisTick: {
      length: 6,
      lineStyle: {
        type: 'dashed'
        // ...
      }
    }
  }
  // ...
};
```


5. 图例：

图例是图表中对内容区元素的注释、用不同形状、颜色、文字等来标示不同数据列，通过点击对应数据列的标记，可以显示或隐藏该数据列的配置项，在echarts中使用 legend 配置项标识。

图例虽然不是图表中的主要信息、却是了解图表信息的钥匙。它一般放在图表的右上角、也可以放在图表的底部、同一页面中的所有图例位置保持一致，可以横排对齐也可以纵排对齐。等等看具体需求。

- 图例常见配置
```
option = {
  <!-- 图例配置 -->
  legend: {
    type: 'scroll',
    orient: 'vertical',
    right: 10,
    top: 20,
    bottom: 20,
    data: ['图例一', '图例二', '图例三' /* ... */, , '图例n']
    backgroundColor: '#ccc',
    textStyle: {
      color: '#ccc'
      // ...
    }
  }
  // ...
};
```
**注意**：当图表只有一种数据信息时，用图表标题说明数据信息即可。建议此时不要加上图例。

# 三、echarts常用图表学习
## 3.1 柱状图
>柱状图（或称条形图）是一种通过柱形的长度来表现数据大小的一种常用图表类型。



```
option = {
  // 图表标题
  title: {
    text: '第一个 ECharts 实例'
  },
  // 提示信息
  tooltip: {},
  // 图例组件
  legend: {},
  // x轴的配置
  xAxis: {
    // 轴线配置
    axisLine: {
      // 两端的箭头
      symbol: 'arrow',
      // 轴线的样式
      lineStyle: {
        // 轴线类型
        type: 'dashed'
      }
    },
    // 坐标轴上的刻度配置
    axisTick: {
      // 刻度长度
      length: 6,
      lineStyle: {
        type: 'dashed'
      }
    }
    // 刻度线上的标签
    axisLabel: {
      formatter: '{value} kg',
      align: 'center'
    }
  },
  yAxis: { // y轴的配置和x轴是一样的}
  // 每个系列通过 type 决定自己的图表类型
  series: {
    type: 'scatter',

    // 普通样式。
    itemStyle: {
      // 点的颜色。
      color: 'red'
    },
    label: {
      show: true,
      // 标签的文字。
      formatter: 'This is a normal label.'
    },

    // 高亮样式中的结构，和普通样式的结构相同。在鼠标悬浮到图形元素上时显示
    emphasis: {
      itemStyle: {
        // 高亮时点的颜色。
        color: 'blue'
      },
      label: {
        show: true,
        // 高亮时标签的文字。
        formatter: 'This is a emphasis label.'
      }
    }
  }
};
```