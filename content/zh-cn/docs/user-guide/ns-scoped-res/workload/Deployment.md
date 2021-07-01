---
title: "Deployment"
weight: 1
---

本文档介绍了如何在 KubeCube 上创建、使用和编辑 Deployment。

## 准备工作

创建一个租户，在租户下创建一个项目，在项目下创建一个命名空间，创建一个账号并赋予该命名空间操作权限。

## 创建 Deployment

1、选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击 【Deployments】，进入 Deployment 管理页面。

![部署deploy](/imgs/产品使用指南/K8s资源管理/工作负载管理/Deployment/部署deploy.png)

2、点击【部署】，编写 deployment 的 yaml 文件。点击【确定】，即开始部署该 deployment。

![yaml设置deloy](/imgs/产品使用指南/K8s资源管理/工作负载管理/Deployment/yaml设置deloy.png)

deployment 的 规范可参考：https://v1-20.docs.kubernetes.io/docs/concepts/workloads/controllers/deployment/。

## 管理 Deployment

选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击【Deployments】，进入 Deployment 管理页面，可以看到该命名空间下的所有 deployment 名称以及状态。

这里的状态指的是该 deployment 下所有副本的状态。

- desired：预期的副本数；
- updated：已经是最新版本的副本数；
- available：可用副本数；
- unavailable：不可用副本数；
- total：总副本数。

同时也可以根据名称对列表进行搜索，或对单个 deployment 进行副本数调整、滚动更新、删除，以及修改 Yaml。

![deploy管理页面](/imgs/产品使用指南/K8s资源管理/工作负载管理/Deployment/deploy管理页面.png)

## 查看 Deployment 详情

在 Deployment 管理页面，点击任一 deployment 名称，可以进入到该 deployment 详情页。

![deploy详情页](/imgs/产品使用指南/K8s资源管理/工作负载管理/Deployment/deploy详情页.png)

在 Deployment 详情页，可以查看到 deployment 的具体信息，以及该 deployment 所关联的所有副本的详情、副本的监控数据以及该 deployment 和副本的事件信息和 condition 信息。