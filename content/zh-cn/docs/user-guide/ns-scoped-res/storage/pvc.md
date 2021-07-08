---
title: "PVC"
weight: 1
---

本文档介绍了如何在 KubeCube 上创建、使用和编辑 PVC（Persistent Volume Claim）。

## 准备工作

创建一个租户，在租户下创建一个项目，在项目下一个创建一个命名空间，创建一个账号并赋予该命名空间操作权限。

## 创建 PVC

1、选择租户和项目，选择集群和空间，点击【存储】菜单，进入 PVC 管理页面。在管理页面可以进行 PVC 记录的添加、设置、删除和 Yaml 设置。

![manage1.png](/imgs/user-guide/ns-scoped-res/storage/pvc/manage1.png)

2、点击【创建存储声明】，弹出创建存储声明的窗口。

![create.png](/imgs/user-guide/ns-scoped-res/storage/pvc/create.png)

- 存储类别：选择存储类别，存储类别可以在集群管理页面添加
- 名称：存储声明名称
- 容量：填写所需存储容量
- 模式：独占读写（ ReadWriteOnce：读写权限，并且只能被单个节点挂载 ）、只读共享（ ReadOnlyMany：只读权限，允许被多个节点挂载 ）、共享读写（ ReadWriteMany：读写权限，允许被多个节点挂载 ）

## 查看 PVC 详情

在 PVC 管理页面选择一条 PVC 记录，可以查看该 PVC 记录的详细信息，可以查看绑定的副本信息和监控信息。并且可以管理操作，包括删除、设置和 Yaml 设置。

![detail.png](/imgs/user-guide/ns-scoped-res/storage/pvc/detail.png)

