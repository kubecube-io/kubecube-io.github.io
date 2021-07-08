---
title: "Ingress"
weight: 2
---

本文档介绍了如何在 KubeCube 上创建、使用和编辑 Ingress。KubeCube 默认使用 Nginx Ingress。

## 准备工作

创建一个租户，在租户下创建一个项目，在项目下一个创建一个命名空间，命名空间下创建一个 Service，创建一个账号并赋予该命名空间操作权限。

## 创建 Ingress

1、选择租户和项目，选择集群和空间，展开【服务与发现】菜单，点击【Ingresses】菜单按钮，进入 Ingress 管理页面。

![manage.png](/imgs/user-guide/ns-scoped-res/service-discovery/ingress/manage.png)

2、点击【创建负载均衡】按钮，进入创建负载均衡页面，填写信息后，点击【立即创建】按钮，即可创建一个 Ingress。

![create.png](/imgs/user-guide/ns-scoped-res/service-discovery/ingress/create.png)

- 名称：输入 Ingress 名称

- 端口：选择对外暴露访问的端口
- 调度算法：选择负载均衡轮询策略
- 转发规则：设置 Host，设置 Path 与 Service 端口的映射关系，可以添加多条转发规则
- 会话保持：开通/关闭会话保持

## 管理 Ingress

选择租户和项目，选择集群和空间，展开【服务与发现】菜单，点击【Ingresses】菜单按钮，进入 Ingress 管理页面，可以对 Ingress 列表进行设置重编辑，删除和 Yaml 设置。

![manage.png](/imgs/user-guide/ns-scoped-res/service-discovery/ingress/manage.png)

## 查看 Ingress 详情

在 Ingress 管理页面，点击具体一条 Ingress 记录的名称，进入详情页面。

![ingressdetail.png](/imgs/user-guide/ns-scoped-res/service-discovery/ingress/ingressdetail.png)

Ingress 详情页面除了可以管理 Ingress，还可以查看 Ingress 的详细信息、关联的 Service 信息和事件信息。

 