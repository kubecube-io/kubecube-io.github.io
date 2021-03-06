---
title: "日志查询"
weight: 2
---

## 搜索模式
#### 查询条件
如下图所示，支持按以下条件筛选日志任务，查询日志数据：

![日志查询](/imgs/user-guide/logs/logsearch.png)

- 日志任务：选择在日志任务管理中创建的日志采集任务；
- 查询语句：输入查询语句，示例：status:200 AND extension:PHP；
- 筛选条件：选择Key-Value值，可组合条件，快速筛选所需日志数据；
- 时间范围：
  - 默认查询时间范围为近1小时；
  - 提供快捷选项，可快速选择日志查询时间范围；
  - 自定义时间：自定义选择开始日期和结束日期；点击【切换】，可自定义填写具体的开始时间和结束时间，时间粒度最小为秒级。

#### 日志柱形图
返回日志数据结果后，日志查询页面将展示日志柱形图。日志柱形图是时间横轴、日志采集数纵轴组成的蓝色柱形图表。根据蓝色柱形的波动可以直观看出该时间段内日志产生数量的变化趋势。如果当前日志为服务器的访问日志，就可以快速地发现这段时间内整个服务的负载情况以及用户访问情况。  
日志柱状图还提供了丰富的交互，用户可快速定位日志。点击柱形即可定位到更细粒度的时间区间；或者拖动选择所需查找的时间区间，前端立即返回该时间段内的日志数据。  
鼠标移动至柱形，即可展示该柱形对应日志的入库时间以及日志数目。  
日志柱形图上方展示完整时间区间内的日志采集条数，并支持自定义柱形图展示粒度，最小粒度为秒级。  


#### 日志内容展示
设置完成日志查询条件后，前端界面即可展示日志内容。左侧为日志采集时间，可正逆序排列。右侧为日志内容，点击左上方组件，可切换日志内容展示形式，提供的形式有原始日志以及Json形式。  

**自定义日志内容展示列**  
![日志查询感兴趣字段](/imgs/user-guide/logs/logsearch-fields.png)  
用户可自定义感兴趣字段，展示或者隐藏不必要的列信息。  
如图所示，用户可按需选择字段，或输入字段名称进行模糊搜索，选择完成后，前端界面将展示用户所选字段的内容列。比如，选择`namespace`和`pod_name`这个两个字段后，可在日志查询页面看到日志的`namespace`和产生的`pod_name`信息，便于排查问题。  

## 实时流模式
实时流模式可用于上线场景，用户可实时关注与跟踪日志数据。通过设置刷新频率，前端界面实时滚动输出日志数据。  