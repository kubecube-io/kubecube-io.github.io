---
title: "多集群管理"
weight: 1
---

KubeCube 提供多集群管理的能力，可以基于管控集群添加或者删除集群，并对所有接管的集群提供统一的认证和鉴权入口

> ⚠️ 计算集群信息不允许修改，若有修改需求，请先删除计算集群再重新添加，该操作存在的一定风险，删除计算集群期间，计算集群所有资源不受管控集群管控，认证和鉴权功能暂时关闭，直到该集群被重新添加

## 查看集群信息

选择集群查看对应的基本信息，Node、StorageClass、NetworkPolicy 以及 PV

![cluster-inspect](/imgs/user-guide/administration/k8s-cluster/multi-k8s-cluster-mgr/cluster-inspect.png)

## 添加计算集群

[部署新集群并添加](https://www.kubecube.io/docs/installation-guide/add-member-k8s/#方式一部署新集群并添加)

[纳管已有集群](https://www.kubecube.io/docs/installation-guide/add-member-k8s/#方式二纳管已有集群)

## 删除计算集群

点击【删除配置】来删除计算集群，管控集群无法通过 Console 删除。删除计算集群意味着 KubeCube 控制面不再接管该集群，该集群恢复被接管前的样子，集群上运行的工作负载、服务等不会受到影响，可以通过添加该集群来重新接管

![cluster-manager](/imgs/user-guide/administration/k8s-cluster/multi-k8s-cluster-mgr/cluster-manager.png)





