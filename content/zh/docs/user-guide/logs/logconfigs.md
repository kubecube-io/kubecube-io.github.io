---
title: "采集任务管理"
weight: 1
---


> 采集任务管理，用于采集Kubernetes集群中容器的日志，包括容器的标准输出和容器中的日志文件。


## 创建日志采集任务

![日志采集任务列表](/imgs/user-guide/logs/logconfig-list.png)
如图所示，点击【创建日志任务】，创建指定服务的日志采集任务。

**基础配置：**

基础配置部分如图所示：  
![日志采集任务基础配置](/imgs/user-guide/logs/logconfig-create-stdout-base.png)

- 日志任务名称：可任意填写服务的日志采集任务名，比如nginx等；

- 日志源类型：
  - 容器标准输出：容器中的标准输出流；
  - 容器日志：容器内产生的日志文件；

- 标签选择器：标签选择器类似Kubernetes中的LabelSelector，用于指定日志采集任务匹配的Pods，需要和需要被采集日志的Pods中Label保持一致；

- 日志采集路径：如果选择采集容器日志文件，需要输入日志路径，路径为glob表达式的形式，例如：`/var/log/*.log`。另外需注意的是，如果填写`/var/log`，不会采集目录下的所有文件，会被认为是采集`/var`目录下的`log`文件。如果填写`/var/log/*`，则会采集`/var/log`下本级的目录，如果需要遍历，可以填写`/var/log/**`；

**高级配置：**

![日志采集任务高级配置1](/imgs/user-guide/logs/logconfig-create-advance-1.png)

- 容器：如果Pod中有多个容器，则建议指定具体的容器名称，否则会给采集所有容器下的日志；

- 元信息/注入Pod标记：在日志配置中注入Pod的label(标签)、env(环境变量)、annotation(注解)，可用作日志查询页面的筛选条件；

- 元信息/自定义标记：自定义Key-Value值，可用作日志查询页面的筛选条件；

![日志采集任务高级配置1](/imgs/user-guide/logs/logconfig-create-advance-2.png)

- 日志多行配置：日志多行配置用于指定处理跨多行消息的处理方式
  - pattern：指定用于匹配多行的正则表达式；
  - negate：定义模式是否被否定；
  - match：指定如何把多行合并成一条；

- 单条日志大小上限：避免单行日志太大会导致日志采集Agent OOM异常等；

- 排除日志：该路径下的文件将被忽略，日志内容不被收集；支持正则匹配，建议排除压缩文件，例如：\.gz$；

- 忽略日志文件：将忽略日志任务创建时间起对应时间段内的日志文件；

- 日志保留：可指定日志保留文件数或日志保留天数，日志Agent会帮助定时清理；

## 更新日志采集任务
在日志任务管理的列表中，点击`操作`列的`设置`即可，更新字段可参考上面创建部分。

## 删除日志采集任务
在日志任务管理的列表中，点击`操作`列的`删除`即可，删除后不再采集匹配的Pod的日志。