---
title: "网络策略(NetworkPolicy)"
weight: 4
---

[NetworkPolicy](https://kubernetes.io/zh/docs/concepts/services-networking/network-policies/) 依赖 CNI 实现，创建一个 NetworkPolicy 资源对象而没有控制器来使它生效的话，是没有任何作用的，KubeCube 默认使用 calico


## 查看 NetworkPolicy

点击【集群管理】，选择要操作的集群，点击【网络策略】，点击【查看详情】可以查看 NetworkPolicy 的详细描述，点击【设置】可以对 NetworkPolicy 进行修改，点击【删除】可以删除该资源

![view-networkpolicy](/imgs/user-guide/administration/k8s-cluster/cluster-scoped-res/NetworkPolicy/view-networkpolicy.png)

## 创建 NetworkPolicy

点击【创建网络策略】可以创建新的 NetworkPolicy

![create-networkpolicy](/imgs/user-guide/administration/k8s-cluster/cluster-scoped-res/NetworkPolicy/create-networkpolicy.png)
