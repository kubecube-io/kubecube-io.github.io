---
title: "配额管理"
weight: 4
---

KubeCube 在 Kubernetes 原生的资源配额能力上进行了拓展，在租户层级即可对资源配额进行限制，在使用体验上与 Kubernetes 原生的 ResouceQuota 保持一致

> KubeCube 目前支持对 nvidia gpu 进行资源配额

## 资源配额结构
![resourcequota.png](/imgs/user-guide/administration/quota/resourcequota.png)

资源配额的计算结构遵循以下约束：

1. 租户下 namespace 的资源配额总和 < 租户配额
2. 集群下租户的资源配额总和 < 集群的物理资源

> CubeResourceQuota 是 KubeCube 对于 namespace 级别的 ResourceQuota 的上层抽象，基于 CRD 实现

## Tenant 资源配额

### 前置要求
创建一个租户

### 设置租户的资源配额

选择租户，点击【调整配额】对指定集群下的租户进行资源配额的设置

![tenant-quota.png](/imgs/user-guide/administration/quota/tenant-quota.png)

在可填框中填入期望设置的资源配额，点击【确定】保存配额设置

- 【集群可分配】表示该集群剩余可分配资源
- 【租户已分配】表示该租户下所有 namespace 已分配的资源总和

![modify-tenant-quota.png](/imgs/user-guide/administration/quota/modify-tenant-quota.png)

## 创建 Namespace 并设置资源配额

### 前置要求
创建一个租户，在租户下创建项目

### 设置 namespace 的资源配额

点击右上方【租户】选择框，选择租户，点击【创建空间】创建新的 namespace 并设置资源配额，也可以点击【修改】对已创建的 namespace 的资源配额进行修改

![ns-quota.png](/imgs/user-guide/administration/quota/ns-quota.png)

创建 namespace 时，需要选择空间所属的集群、租户、项目，namespace 一旦创建，其所属关系不能更改。在可填框中填入期望的资源配额，点击【确定】使资源配额生效

【租户可分配】表示该 namespace 所属的租户所剩的可分配资源配额

![modify-ns-quota.png](/imgs/user-guide/administration/quota/modify-ns-quota.png)

