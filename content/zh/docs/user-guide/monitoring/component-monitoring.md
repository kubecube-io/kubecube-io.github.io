---
title: "平台组件监控"
weight: 3
---

本文档介绍了如何在 KubeCube 中查看平台核心组件监控。

## 准备工作

以平台管理员角色登录 KubeCube 平台。

## 查看组件监控视图

当前 KubeCube 平台支持对以下组件的监控视图可视化查询:

- 管控面 Pod 监控
- CoreDNS 监控
- Etcd 监控
- Kube ApiServer 监控
- Kube Controller Manager 监控
- Kube Proxy 监控
- Kube Scheduler 监控
- Kubelet 监控
- Prometheus 监控
- Thanos Query 监控

可以登录到 KubeCube 平台，点击【运维管理】，侧边栏选择【组件监控】，进行查看

![component-monitoring](/imgs/user-guide/monitoring/component-monitoring.png)

点击需要查看的组件，即可查看对应的监控视图，以平台组件 pod 监控为例，点击【control-plane-pods】后，可以查看不同集群中，管控组件 Pod 的资源使用情况；对于其他组件，可以查看相应的核心指标监控。

![control-plane-pods](/imgs/user-guide/monitoring/control-plane-pods.png)

