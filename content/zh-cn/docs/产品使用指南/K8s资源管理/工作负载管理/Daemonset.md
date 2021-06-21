---
title: "DaemonSet"
weight: 7
---

本文档介绍了如何在 KubeCube 上创建、使用和编辑 DaemonSet。

## 准备工作

创建一个租户，在租户下创建一个项目，在项目下创建一个命名空间，创建一个账号并赋予该命名空间操作权限。

## 创建 DaemonSet

1、选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击 【DaemonSets】，进入 DaemonSet 管理页面。

![部署daemonset](/imgs/产品使用指南/K8s资源管理/工作负载管理/DaemonSet/部署daemonset.png)

2、点击【部署】，编写 daemonSet 的 yaml 文件。点击【确定】，即开始部署该 daemonSet。

daemonSet 的 规范可参考：https://v1-20.docs.kubernetes.io/zh/docs/concepts/workloads/controllers/daemonset/。	

注意，daemonSet 的 namespace 应与当前所在 namespace 一致。

## 管理 DaemonSet

选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击【DaemonSets】，进入 DaemonSet 管理页面，可以看到该命名空间下的所有 daemonSet，包括名称、级别以及创建时间。

同时也可以根据名称对列表进行搜索，或对单个 daemonSet 进行删除或修改。

![管理daemonset](/imgs/产品使用指南/K8s资源管理/工作负载管理/DaemonSet/管理daemonset.png)

## 查看 DaemonSet 详情

在 DaemonSet 管理页面，点击任一 daemonSet 名称，可进入到该 daemonSet 详情页。

![daemonSet详情页](/imgs/产品使用指南/K8s资源管理/工作负载管理/DaemonSet/daemonset详情页.png)

在 DaemonSet 详情页，可以查看到 daemonSet 的具体信息，以及该 daemonSet 所关联的所有副本的详情、副本的监控数据以及该 daemonSet 和副本的事件信息和 condition 信息。