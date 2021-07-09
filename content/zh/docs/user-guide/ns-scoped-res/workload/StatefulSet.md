---
title: "StatefulSet"
weight: 2
---

本文档介绍了如何在 KubeCube 上创建、使用和编辑 StatefulSet。

## 准备工作

创建一个租户，在租户下创建一个项目，在项目下一个创建一个命名空间，创建一个账号并赋予该命名空间操作权限。

## 创建 StatefulSet

1、选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击【Statefulsets】，进入StatefulSet 管理页面。

![statefulset-apply](/imgs/user-guide/ns-scoped-res/workload/StatefulSet/statefulset-apply.png)

2、点击【部署】，编写 statefulset  的 yaml 文件。点击【确定】，即可部署该 statefulset。

statefulset 的规范可参考：https://v1-20.docs.kubernetes.io/docs/concepts/workloads/controllers/statefulset/。

## 管理 StatefulSet

选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击【Statefulset】，进入 StatefulSet 管理页面。在管理页面，可以看到该命名空间下的所有 statefulSet。

同时也可以根据名称对列表进行搜索，或对单个 statefulset 进行副本数调整、删除，以及修改 Yaml。

![statefulset-manage](/imgs/user-guide/ns-scoped-res/workload/StatefulSet/statefulset-manage.png)

## 查看 StatefulSet 详情

在 StatefulSet 管理页面，点击任一 statefulSet 名称，即可进入到该 statefulSet 详情页。

![statefulset-detail](/imgs/user-guide/ns-scoped-res/workload/StatefulSet/statefulset-detail.png)

StatefulSet 详情页除了可以管理 StatefulSet，还可以查看 StatefulSet 的详细信息，关联的副本信息和副本的监控数据，以及  StatefulSet 和关联副本的事件和 condition 信息。