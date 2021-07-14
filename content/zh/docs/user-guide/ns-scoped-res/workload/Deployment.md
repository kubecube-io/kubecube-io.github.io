---
title: "Deployment"
weight: 1
---

本文档介绍了如何在 KubeCube 上创建、使用和编辑 Deployment。

## 准备工作

创建一个租户，在租户下创建一个项目，在项目下创建一个命名空间，创建一个账号并赋予该命名空间操作权限。

## 创建 Deployment

1、选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击 【Deployments】，进入 Deployment 管理页面。

![deploy-apply](/imgs/user-guide/ns-scoped-res/workload/Deployment/deploy-apply.png)

2、点击【部署】，进入创建 Deployment 页面，填写信息后，点击【立即创建】，即可创建一个 Deployment。

- 基本信息
    - 名称：由小写字母、数字或中划线组成，长度1～63位，以字母开头，字母或数字结尾；
    - 副本数：默认为1个；
    - 更新策略
        - 最短就绪时间：新创建的副本准备就绪后，被视为可用前需要保持正常的时间下限，单位（秒）；
        - 最大超预期副本数：可创建的最大超过所需副本的副本数量或百分比；
        - 最大不可用副本数：更新过程中不可使用的副本数上限个数或百分比；
- 容器配置
    - 容器名称：副本运行的容器名称；
    - 镜像：容器运行的[镜像](https://www.kubecube.io/docs/user-guide/registry/registry) ；
    - 配置：配额设置，包括请求配额（基础配置）、最大配额（配置上限），以及 GPU 配置；
    - 挂载数据卷：选择已创建的数据卷,并设置挂载目录和子路径等；
    - 环境变量：配置副本的环境变量；
    - 容器类型：分为业务容器和 init 容器，init 容器不支持就绪探针，必须可以执行结束。一个 pod 可以有多个 init 容器，它们将依次在业务容器运行前执行。
    - 启动命令：容器启动时即执行该命令；
    - 启动命令参数：执行启动命令时所需的参数；
    - 存活探针：可以开启存活探测器，并配置，具体配置方法可参考 https://v1-19.docs.kubernetes.io/zh/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/。
    - 就绪探针：可以开启就绪探测器，并配置，具体配置方法可参考 https://v1-19.docs.kubernetes.io/zh/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/。
    - 生命周期-停止前：在容器因 API 请求或者管理事件（诸如存活态探针、启动探针失败、资源抢占、资源竞争等） 而被终止之前，此命令会被调用。
    - 生命周期-启动后：这个回调在容器被创建之后立即被执行。 但是，不能保证回调会在容器入口点（ENTRYPOINT）之前执行。 没有参数传递给处理程序。
    - 容器端口：需要在副本的 IP 地址上公开的端口；
    - 镜像拉取策略：可以选择 Always、Never、IfNotPresent；
- 高级配置
    - 仓库密钥：如果配置的镜像需要密钥拉取，选择已创建的 Secret；
    - 标签：给该 Deployment 添加标签；
    - 注释：给该 Deployment 添加注释；
    - 部署策略：给该 Deployment 添加节点亲和性、副本亲和性、副本反亲和性、容忍等部署规则。
    
## 管理 Deployment

选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击【Deployments】，进入 Deployment 管理页面，可以看到该命名空间下的所有 deployment 名称以及状态。

这里的状态指的是该 deployment 下所有副本的状态。

- desired：预期的副本数；
- updated：已经是最新版本的副本数；
- available：可用副本数；
- unavailable：不可用副本数；
- total：总副本数。

同时也可以根据名称对列表进行搜索，或对单个 deployment 进行副本数调整、滚动更新、删除，以及修改 Yaml。

![deploy-manage](/imgs/user-guide/ns-scoped-res/workload/Deployment/deploy-manage.png)

## 查看 Deployment 详情

在 Deployment 管理页面，点击任一 deployment 名称，可以进入到该 deployment 详情页。

![deploy-detail](/imgs/user-guide/ns-scoped-res/workload/Deployment/deploy-detail.png)

在 Deployment 详情页，可以查看到 deployment 的具体信息，以及该 deployment 所关联的所有副本的详情、副本的监控数据以及该 deployment 和副本的事件信息和 condition 信息。