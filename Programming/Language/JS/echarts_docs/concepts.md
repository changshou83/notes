## 图标容器及大小

初始化：

容器自带宽高

通过init函数的参数指定宽高：`echarts.init(dom, null, { width, height })`

<br/>

响应容器大小的变化：

跟随容器：`window.addEventListener('resize', () => myChart.resize())`或者用ResizeObserver来进行更细粒度的监听

不跟随容器：`myChart.resize({ width, height })`

<br/>

容器被销毁以及被重建时：

容器销毁后调用`myChart.dispose`销毁实例，避免内存泄漏。容器重新添加后再次调用`echarts.init`

## 样式(itemStyle)

四种方式设置样式

主题：

使用：`echarts.init(dom, theme)`

注册：`echarts.registerTheme(name, content)`

<br/>

调色盘

<br/>

直接样式设置：直接在`option`里面配置

普通样式：`itemStyle`，`label`等

高亮样式：`emphasis`下的`itemStyle`，`label`等

<br/>

视觉映射

## 数据集(Dataset)

管理数据，提倡数据与配置分离

简单示例：

```javascript
option = {
  dataset: {
    // 提供一份数据
    source: [
      ['product', '2015', '2016', '2017'], // x轴默认对应到dataset第一列
      ['Matcha Latte', 43.3, 85.8, 93.7], // 每个系列默认对应到dataset的每一列
      ['Milk Tea', 83.1, 73.4, 55.1],
      ['Cheese Cocoa', 86.4, 65.2, 82.5],
      ['Walnut Brownie', 72.4, 53.9, 39.1]
    ]
  },
  xAxis: { type: 'category' },
  yAxis: {},
  series: [{ type: 'bar' }, { type: 'bar' }, { type: 'bar' }]
}
```

### 数据集到系列的映射

`series.seriesLayoutBy`指定数据集的行还是列映射为系列，默认按列映射

### 维度(dimension)

维度是数据项的分类依据。例如将二维表的行映射到系列时，行就是维度，列就是数据项。

`dataset.dimensions`定义维度，可以指定名称和类型。

### 数据到图形的映射

`series.encode`指定维度(一个行/列)映射到坐标轴(xAxis，yAxis)，提示框(tooltip)，标签(label)，图形元素大小(virtualMap)等的规则

### 视觉通道的映射

`series.visualMap`

### 多个dataset.source如何被系列引用

`series.datasetIndex`

### 限制

只有某些图形支持dataset，数据量大时仍使用appendData，此情况不支持dataset

## 数据转换(dataset.transform)

给定一个数据集和一个转换方法，生成一个新的数据集

例如将一份数据按照年份展示

```javascript
var option = {
  dataset: [
    {
      // 这个 dataset 的 index 是 `0`。
      source: [
        ['Product', 'Sales', 'Price', 'Year'],
        ['Cake', 123, 32, 2011],
        ['Cereal', 231, 14, 2011],
        ['Tofu', 235, 5, 2011],
        ['Dumpling', 341, 25, 2011],
        ['Biscuit', 122, 29, 2011],
        ['Cake', 143, 30, 2012],
        ['Cereal', 201, 19, 2012],
        ['Tofu', 255, 7, 2012],
        ['Dumpling', 241, 27, 2012],
        ['Biscuit', 102, 34, 2012],
        ['Cake', 153, 28, 2013],
        ['Cereal', 181, 21, 2013],
        ['Tofu', 395, 4, 2013],
        ['Dumpling', 281, 31, 2013],
        ['Biscuit', 92, 39, 2013],
        ['Cake', 223, 29, 2014],
        ['Cereal', 211, 17, 2014],
        ['Tofu', 345, 3, 2014],
        ['Dumpling', 211, 35, 2014],
        ['Biscuit', 72, 24, 2014]
      ]
      // id: 'a'
    },
    {
      // 这个 dataset 的 index 是 `1`。
      // 这个 `transform` 配置，表示，此 dataset 的数据，来自于此 transform 的结果。
      transform: {
        type: 'filter',
        config: { dimension: 'Year', value: 2011 }
      }
      // 我们还可以设置这些可选的属性： `fromDatasetIndex` 或 `fromDatasetId`。
      // 这些属性，指定了，transform 的输入，来自于哪个 dataset。例如，
      // `fromDatasetIndex: 0` 表示输入来自于 index 为 `0` 的 dataset 。又例如，
      // `fromDatasetId: 'a'` 表示输入来自于 `id: 'a'` 的 dataset。
      // 当这些属性都不指定时，默认认为，输入来自于 index 为 `0` 的 dataset 。
    },
    {
      // 这个 dataset 的 index 是 `2`。
      // 同样，这里因为 `fromDatasetIndex` 和 `fromDatasetId` 都没有被指定，
      // 那么输入默认来自于 index 为 `0` 的 dataset 。
      transform: {
        // 这个类型为 "filter" 的 transform 能够遍历并筛选出满足条件的数据项。
        type: 'filter',
        // 每个 transform 如果需要有配置参数的话，都须配置在 `config` 里。
        // 在这个 "filter" transform 中，`config` 用于指定筛选条件。
        // 下面这个筛选条件是：选出维度（ dimension ）'Year' 中值为 2012 的所有
        // 数据项。
        config: { dimension: 'Year', value: 2012 }
      }
    },
    {
      // 这个 dataset 的 index 是 `3`。
      transform: {
        type: 'filter',
        config: { dimension: 'Year', value: 2013 }
      }
    },
    // transform可链式使用，内置两种数据转换器：filter和sort
    {
      transform: [
        {
          type: 'filter',
          config: { dimension: 'Product', value: 'Tofu' }
        },
        {
          type: 'sort',
          config: { dimension: 'Year', order: 'desc' }
        }
      ]
    },
    {
      transform: {
        type: 'filter',
        config: {},
        print: true // debug用，transform会在控制台中被打印出来
      }
    }
  ],
  series: [
    {
      type: 'pie',
      radius: 50,
      center: ['25%', '50%'],
      // 这个饼图系列，引用了 index 为 `1` 的 dataset 。也就是，引用了上述
      // 2011 年那个 "filter" transform 的结果。
      datasetIndex: 1
    },
    {
      type: 'pie',
      radius: 50,
      center: ['50%', '50%'],
      datasetIndex: 2
    },
    {
      type: 'pie',
      radius: 50,
      center: ['75%', '50%'],
      datasetIndex: 3
    },
    {
      type: 'pie',
      datasetIndex: 4
    }
  ]
};
```

## 坐标轴(xAxis,yAxis)

x,y轴的一些配置

轴线，刻度，刻度标签，轴标题

## 视觉映射(VisualMap)

连续型视觉映射和分段型视觉映射

## 图例(Legend)

<br/>

## 事件与行为

### 鼠标事件

常见类别：

- click，dblclick
- mousedown，mousemove，mouseup，mouseover，mouseout
- globalout，contextmenu

如何区分鼠标点到了那里？

先通过componentType区分`markPoint`或者`series`

如果是`markPoint`，那就区分一下点的哪个，用`seriesIndex`

如果是`series`，那就区分一下图的类型`seriesType`，区分一下点的哪里，用`dataType`

<br/>

只对某些图形触发回调？

使用query参数：`chart.on(eventName, query, handler)`

### 行为事件

https://echarts.apache.org/zh/api.html#events

### 编程控制行为

`chart.dispatchAction`

### 监听空白处的事件

`chart.getZr().on(type, callback)`
