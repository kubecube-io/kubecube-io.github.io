---
author: 蔡鑫涛
date: 2023-04-10
summary: KubeCube v1.8 版本发布
tags:
- KubeCube
title: KubeCube v1.8 版本发布
---

在本次的 KubeCube 新版本中，日志模块**集成了 [Loggie](https://github.com/loggie-io/loggie)**，它是一个基于Golang的轻量级、高性能、云原生日志采集Agent和中转处理Aggregator，支持多Pipeline和组件热插拔。

在部署方面 KubeCube 提供了**通过 Helm 安装 KubeCube** 的方式，这使得 KubeCube 更加专注容器平台自身。关于 K8s 的安装，KubeCube 保留了通过 All-In-One 部署脚本进行的单节点 K8s 集群的安装。关于自定义的 K8s 集群安装，可以使用 kubeadm、[kubez-ansible](https://github.com/gopixiu-io/kubez-ansible) 等开源工具，之后 kubez-ansible 中也会集成 KubeCube。

除此之外，KubeCube 新增了一些 Features，比如支持自定义启用 controller、支持删除租户项目、支持使用 http 模式启动等特性。在**稳定性方面**，进行了一些 Bug 修复。

## 为什么使用 Loggie

KubeCube 在 1.8 之前使用 filebeat 作为日志 agent，在使用过程中，我们遇到了一些特性的短板，比如容器内部日志文件的采集，多 pipeline 和多输出源，以及在 K8s 场景下，我们更希望通过原生的方式进行动态的配置变更。出于以上考量，我们决定在 1.8 版本使用 loggie 替换 filebeat，loggie 的性能优异，轻量，并且提供了：

- **一栈式日志解决方案**：同时支持日志中转、过滤、解析、切分、日志报警等。
- **云原生的日志形态**：快速便捷的容器日志采集方式，原生的Kubernetes动态配置下发。
- **生产级的特性**：Loggie吸收了我们长期的大规模运维经验，形成了全方位的可观测性、快速排障、异常预警、自动化运维能力。

关于 Loggie 的更多特性以及使用方式，请参考 [Loggie 官方文档](https://loggie-io.github.io/docs/)。

### 使用 hotplug 开启 Loggie

请参考 [开启loggie文档](https://www.kubecube.io/docs/installation-guide/hotplug/open-loggie) 来在 KubeCube 中开启 Loggie 特性。

### 使用 Loggie 进行日志采集

请参考 [日志使用文档](https://www.kubecube.io/docs/user-guide/logs) 来进行采集任务的管理和相关的日志查询。

## 使用 Helm 方式简化 KubeCube 部署

KubeCube 新增了 [kubecube-chart](https://github.com/kubecube-io/kubecube-chart) 工程，用于维护 KubeCube 的部署 chart，用户可以直接使用该工程进行 KubeCube 的管控面和计算面的部署。

KubeCube 新增了 [hotplugs](https://github.com/kubecube-io/hotplugs) 工程，用于维护 KubeCube 所用的热插拔部署包，上文提到的 Loggie，就是作为 KubeCube 的一个热插拔组件，维护在该工程中。后期，用户也可以通过自己的热插拔部署包，使用 KubeCube 的热插拔框架进行能力拓展。

KubeCube 更加专注容器平台自身，提供通过 helm 的方式在已有的 k8s 上安装 KubeCube，同时，为了让用户在裸机上快速体验 KubeCube，KubeCube 保留了 All-In-One 的安装方式，注意，All-In-One 的部署模式，只能用作 POC，不能用于生产。关于 K8s 的安装，社区已经有许多成熟的方案，比如标准的 kubeadm 安装方式，更加自动化的 kubez-ansible 方式等，用户可以根据自己的需求，进行自定义的 K8s 集群安装，随后在已有的 K8s 上安装 KubeCube。

## 稳定性提升

除了上述内容外，KubeCube 还进行了一些问题修复和功能增强。

- 对服务发现接口增加缓存以提升服务发现的性能和稳定性：使用 client-go/discovery/cached/memory 对首次服务发现的结果进行缓存，以缓解每次进行服务发现都需要请求 k8s-apiserver 接口的 IO 瓶颈。
- 优化了用户从属关系判断：原先 KubeCube 通过查询 user 相关 RoleBinding 来判断用户所属的项目租户，优化后通过 RoleBinding Controller 将用户的关系写入 user.Status 中，判断用户的从属关系只需要查询 user.Status 即可，提升了从属判断的准确性和效率。
- 修复了认证 http.Client 连接泄漏问题：在对接第三方认证时，每一次认证请求，都会初始化一个新的 http.Client 句柄，并且再请求完成后没有释放该连接，在请求量大的时候，导致客户端侧端口被用尽。修复后，复用了同一个 http.Client 句柄，并且在连接空闲释放连接。
- 允许自定义 controllers 的启动：KubeCube 拥有若干用于监听 K8s 资源的 controllers，在一些场景下，我们并不需要某些 controllers 工作，此时，全量启动 controllers 会造成资源浪费，新版本中，KubeCube 允许用户指定所需的 conntrollers。
- 优化了代理 filter 接口：KubeCube 代理了 k8s-apiserver 服务，并对外提供类 k8s-apiserver 的接口，同时，KubeCube 增强了接口的处理能力，支持更加丰富的分页、过滤、模糊搜索等能力，新版本中，优化了 filter 的性能，增加了接口响应速度。

## 写在最后

未来我们会持续提供更多功能，帮助企业简化容器化落地。也欢迎大家参与贡献，提出宝贵的建议。添加以下微信进入 KubeCube 交流群。

<img src="/imgs/kubecube-wechat.png" alt="kubecube微信" style="zoom:40%;" />

**作者简介：** 蔡鑫涛，网易数帆轻舟容器平台资深开发，KubeCube Committer，Kubernetes Member

