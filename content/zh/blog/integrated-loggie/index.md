---
author: 蔡鑫涛
date: 2023-04-10
summary: KubeCube v1.8 版本发布
tags:
- KubeCube
title: KubeCube v1.8 版本发布
---

在本次的 KubeCube 新版本中，日志模块**集成了 Loggie**，它是一个基于Golang的轻量级、高性能、云原生日志采集Agent和中转处理Aggregator，支持多Pipeline和组件热插拔。

在部署方面 KubeCube 提供了**通过 Helm 安装 KubeCube** 的方式，这使得 KubeCube 更加专注容器平台自身。关于 K8s 的安装，KubeCube 保留了通过 All-In-One 部署脚本进行的单节点 K8s 集群的安装。关于自定义的 K8s 集群安装，可以使用 kubeadm、[kubez-ansible](https://github.com/gopixiu-io/kubez-ansible) 等开源工具，之后 kubez-ansible 中也会集成 KubeCube。

除此之外，KubeCube 新增了一些 Features，比如支持自定义启用 controller、支持删除租户项目、支持使用 http 模式启动等特性。在**稳定性方面**，进行了一些 Bug 修复。

## 为什么使用 Loggie

Loggie 提供了：

-  **一栈式日志解决方案**：同时支持日志中转、过滤、解析、切分、日志报警等
-  **云原生的日志形态**：快速便捷的容器日志采集方式，原生的Kubernetes动态配置下发
-  **生产级的特性**：Loggie吸收了我们长期的大规模运维经验，形成了全方位的可观测性、快速排障、异常预警、自动化运维能力

关于 Loggie 的更多特性已经使用方式，请参考 [Loggie 官方文档](https://loggie-io.github.io/docs/)。

Loggie 相较于同类别开源项目，除了支持容器的 stdout 外，还支持容器内部的日志文件。在性能和资源占用方面也处于比较优秀的梯队，详情请参考[开源项目比对报告](https://loggie-io.github.io/docs/user-guide/architecture/compare/)。

|                        | Loggie                                                       | Filebeat                                        | Fluentd                     | Logstash             | Flume                |
| :--------------------- | :----------------------------------------------------------- | :---------------------------------------------- | :-------------------------- | :------------------- | :------------------- |
| 开发语言               | Golang                                                       | Golang                                          | Ruby                        | JRuby                | Java                 |
| 多Pipeline             | 支持                                                         | 单队列                                          | 单队列                      | 支持                 | 支持                 |
| 多输出源               | 支持                                                         | 不支持，仅一个Output                            | 配置copy                    | 支持                 | 支持                 |
| 中转机                 | 支持                                                         | 不支持                                          | 支持                        | 支持                 | 支持                 |
| 日志报警               | 支持                                                         | 不支持                                          | 不支持                      | 不支持               | 不支持               |
| Kubernetes容器日志采集 | 支持容器的stdout和容器内部日志文件                           | 只支持容器stdout                                | 只支持容器stdout            | 不支持               | 不支持               |
| 配置下发               | Kubernetes下可通过CRD配置，主机场景配置中心陆续支持中        | 手动配置                                        | 手动配置                    | 手动配置             | 手动配置             |
| 监控                   | 原生支持Prometheus metrics，同时可配置单独输出指标日志文件、发送metrics等方式 | API接口暴露，接入Prometheus需使用额外的exporter | 支持API和Prometheus metrics | 需使用额外的exporter | 需使用额外的exporter |
| 资源占用               | 低                                                           | 低                                              | 一般                        | 较高                 | 较高                 |

### 使用 hotplug 开启 Loggie

KubeCube 使用 hotplug 热插拔方式集成 Loggie，用户可以通过打开 hotplug 中关于日志部分的开关来开启 Loggie。我们有两个时间点可以用来开启 Loggie：

- 在使用 Helm 安装 KubeCube 时，我们可以设置以下 values 来开启 Loggie。

安装管控集群时的参数如下：

```yaml
# pivot-value.yaml
...
global:
  # set "enabled" if wanna open log application.
  hotPlugEnable:
    pivot:
      logseer: "enabled" 
      logagent: "enabled"
      elasticsearch: "enabled"
...
```

安装计算集群时的参数如下：

```yaml
# member-value.yaml
...
global:    
  # set "enabled" if wanna open log application.
  hotPlugEnable:
    common:
      logagent: "enabled"
...
```

- 在使用过程中，我们也可以通过直接修改 hotplug 的方式来开启 Loggie。

修改管控集群的 hotplug 如下：

```bash
kubectl edit hotplug pivot-cluster

# pivot-cluster
apiVersion: hotplug.kubecube.io/v1
kind: Hotplug
metadata:
  name: pivot-cluster
spec:
  component:
  - name: elasticsearch
    namespace: elasticsearch
    pkgName: elasticsearch-7.8.1.tgz
    status: enabled # 将该值设为 enabled 来开启日志
...
  - name: logseer
    status: enabled # 将该值设为 enabled 来开启日志
  - name: logagent
    status: enabled # 将该值设为 enabled 来开启日志
```

修改计算集群的 hotplug 如下：

```bash
kubectl edit hotplug common

# common
apiVersion: hotplug.kubecube.io/v1
kind: Hotplug
metadata:
  name: common
spec:
  component:
...
  - env: |
      clustername: "{{.cluster}}"
      elasticsearch:
        address: x.x.x.x:32200 # 填写管控集群的 es 的 nodeport svc 访问地址，一般为 {nodeIP}:32200
    name: logagent
    namespace: logagent
    pkgName: logagent-1.3.0.tgz
    status: enabled # 将该值设为 enabled 来开启日志
```

### 使用 Loggie 进行日志采集

请参考 [日志使用文档](https://www.kubecube.io/docs/user-guide/logs) 来进行采集任务的管理和相关的日志查询

## 使用 Helm 方式简化 KubeCube 部署

KubeCube 新增了 [kubecube-chart](https://github.com/kubecube-io/kubecube-chart) 工程，用于维护 KubeCube 的部署 chart，用户可以直接使用该工程进行 KubeCube 的管控面和计算面的部署。

KubeCube 新增了 [hotplugs](https://github.com/kubecube-io/hotplugs) 工程，用于维护 KubeCube 所用的热插拔部署包，上文提到的 Loggie，就是作为 KubeCube 的一个热插拔组件，维护在该工程中。后期，用户也可以通过自己的热插拔部署包，使用 KubeCube 的热插拔框架进行能力拓展。

KubeCube 更加专注容器平台自身，提供通过 helm 的方式在已有的 k8s 上安装 KubeCube，同时，为了让用户在裸机上快速体验 KubeCube，KubeCube 保留了 All-In-One 的安装方式，注意，All-In-One 的部署模式，只能用作 POC，不能用于生产。关于 K8s 的安装，社区已经有许多成熟的方案，比如标准的 kubeadm 安装方式，更加自动化的 kubez-ansible 方式等，用户可以根据自己的需求，进行自定义的 K8s 集群安装，随后在已有的 K8s 上安装 KubeCube。

## 写在最后

未来我们会持续提供更多功能，帮助企业简化容器化落地。也欢迎大家参与贡献，提出宝贵的建议。添加以下微信进入 KubeCube 交流群。

<img src="/imgs/kubecube-wechat.png" alt="kubecube微信" style="zoom:40%;" />

**作者简介：** 蔡鑫涛，网易数帆轻舟容器平台资深开发，KubeCube Committer，Kubernetes Member

