---
title: "Secret"
weight: 1
---

本文档介绍了如何在 KubeCube 上创建、使用和编辑 Secret。

Kubernetes Secret 用于存储和管理一些敏感数据，比如密码密钥等敏感信息。它把 Pod 想要访问的加密数据存放到 Etcd 中。然后用户就可以通过在 Pod 的容器里挂载 Volume 的方式或者环境变量的方式访问 Secret 里保存的信息。

## 准备工作

创建一个租户，在租户下创建一个项目，在项目下一个创建一个命名空间，创建一个账号并赋予该命名空间操作权限。

## 创建 Secret

1、选择租户和项目，选择集群和空间，展开【配置】菜单，点击【Secret】菜单按钮，进入 Secret 管理页面。

![manage.png](/imgs/user-guide/ns-scoped-res/config/secret/manage.png)

2、点击【创建 Secret】按钮，进入创建 Secret 页面，填写信息后，点击【立即创建】按钮，即可创建一个 Secret。

![create.png](/imgs/user-guide/ns-scoped-res/config/secret/create.png)

- 名称：输入 Secret 名称。
- 类型：
  - Opaque：base64 编码格式的 Secret，用来存储密码、密钥等。
  - DockerConfigJson ： 用来存储私有 docker registry 的认证信息。
  - IngressTLS：配置 Ingress TLS 密钥。
- 根据选择的类型，输入相应的信息。

## 查看 Secret 详情

在 Secret 管理页面，点击具体一条 Secret 记录的名称，进入详情页面查看 Secret 的详细信息。

![detail.png](/imgs/user-guide/ns-scoped-res/config/secret/detail.png)

 





