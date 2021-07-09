---
title: "角色管理"
weight: 3
---

KubeCube 的角色管理基于 Kubernetes 的 RBAC 实现，对多集群提供了统一的认证鉴权功能

## 内置角色

KubeCube 针对不同的层级，内置了相应的管理员角色和只有读权限的 reviewer 角色

| 权限\角色    | platform-admin | tenant-admin | project-admin | reviewer |
| ------------ | -------------- | ------------ | ------------- | -------- |
| 集群管理     | ✓              |              |               |          |
| 角色管理     | ✓              |              |               |          |
| 角色查看     | ✓              | ✓             | ✓              |          |
| 用户管理     | ✓              |              |               |          |
| 节点管理     | ✓              |              |               |          |
| 所有租户管理  | ✓              |              |               |          |
| 所有租户成员管理  | ✓              |              |               |          |
| 本租户管理    | ✓              | ✓            |               |          |
| 本租户成员管理 | ✓              | ✓            |               |          |
| 所有项目管理     | ✓              |              |               |          |
| 所有项目成员管理     | ✓              |              |               |          |
| 租户下项目管理     | ✓              | ✓             |               |          |
| 租户下项目成员管理     | ✓              | ✓             |               |          |
| 本项目管理     | ✓              | ✓             | ✓              |          |
| 本项目成员管理     | ✓              | ✓             | ✓              |          |
| 管理 namespace    | ✓ |   |   |          |
| 管理工作负载    | ✓ | ✓ | ✓ |          |
| 管理卷        | ✓ | ✓ | ✓ |          |
| 管理 service        | ✓ | ✓ | ✓ |          |
| 管理 ingress        | ✓ | ✓ | ✓ |          |
| 管理 secrets        | ✓ | ✓ | ✓ |          |
| 管理 serviceaccout        | ✓ | ✓ | ✓ |          |
| 管理 subnamespaceanchor        | ✓ | ✓ | ✓ |          |
| 查看工作负载    | ✓ | ✓ | ✓ | ✓ |
| 查看卷        | ✓ | ✓ | ✓ | ✓ |
| 查看 service        | ✓ | ✓ | ✓ | ✓ |
| 查看 ingress        | ✓ | ✓ | ✓ | ✓ |
| 查看 secrets        | ✓ | ✓ | ✓ | ✓ |
| 查看 serviceaccout        | ✓ | ✓ | ✓ | ✓ |
| 查看 subnamespaceanchor        | ✓ | ✓ | ✓ |✓|

> KubeCube 使用 [HNC](https://github.com/kubernetes-sigs/hierarchical-namespaces) 来实现 tenant、project 和 namespace 的层级，以及彼此之间的隔离；为了实现 namespace 层级的隔离，除 platform-admin 外所有角色，通过 subnamespaceanchor 资源来管理 namespace 

## 管理角色

通过角色标签栏来选择角色层级，点击【添加角色】来新建自定义角色，【继承已有】会以本层级的 admin 角色为模版新建出角色，【自定义】可以自定义编辑新角色

![add-role.png](/imgs/user-guide/administration/role/add-role.png)

通过勾选具体的权限项来自定义角色的权限，点击【修改】提交修改

![mgr-role.png](/imgs/user-guide/administration/role/mgr-role.png)

