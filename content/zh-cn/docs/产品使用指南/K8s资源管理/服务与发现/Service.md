---
title: "Service"
weight: 1
---

本文档介绍了如何在 KubeCube 上创建、使用和编辑 Service。

## 准备工作

创建一个租户，在租户下创建一个项目，在项目下一个创建一个命名空间，在命名空间下创建一个 Deployment，创建一个账号并赋予该命名空间操作权限。

## 创建 Service

1、选择租户和项目，选择集群和空间，展开服务与发现菜单，点击 Services 菜单按钮，进入 Service 管理页面。

![manage1](/imgs/k8sres/service/manage1.png)

2、点击创建服务按钮，进入创建服务页面，填写信息后，点击立即创建按钮，即可创建一个 Service。

![create](/imgs/k8sres/service/create.png)

- 名称：输入服务名称

- 类型：选择服务类型为 ClusterIP 或者 NodePort
- 使用方式：对于 ClusterIP 类型，需要选择使用方式为常规服务、Headless 服务或外部服务
- Selector：选择关联的工作负载，支持高级自定义
- 标签：定义标签
- Ports：添加应用端口与服务端口的映射关系
- 会话保持：开通/关闭会话保持

## 管理 Service

选择租户和项目，选择集群和空间，展开服务与发现菜单，点击 Services 菜单按钮，进入 Service 管理页面，可以对 Service 列表进行设置重编辑，删除和 Yaml 设置。

![manage2](/imgs/k8sres/service/manage2.png)

## 查看 Service 详情

在 Service 管理页面，点击具体一条服务名称，进入 Service 详情页面。

![servicedetail](/imgs/k8sres/service/servicedetail.png)

Service 详情页面除了可以管理 Service，还可以查看 Service 的详细信息、关联的副本信息和事件信息，支持设置 Nginx Ingress 类型的对外服务端口供外部访问。

 