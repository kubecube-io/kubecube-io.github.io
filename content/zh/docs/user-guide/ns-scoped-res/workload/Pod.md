---
title: "Pod"
weight: 5
---

本文档介绍了如何在 KubeCube 上管理 Pods。

## 准备工作

创建一个租户，在租户下创建一个项目，在项目下一个创建一个命名空间，创建一个账号并赋予该命名空间操作权限。

## 管理 Pod

选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击【Pod】，进入 Pod 管理页面。在管理页面，可以看到该命名空间下的所有 pod，包括每个 pod 的名称、IP、状态、重启次数、CPU 使用量、内存使用量以及创建时间。

同时也可以根据名称对列表进行搜索，或对单个 pod 进行查看和删除。

![pod-manage](/imgs/user-guide/ns-scoped-res/workload/Pod/pod-manage.png)