---
title: "集群节点(Node)"
weight: 3
---

KubeCube 支持集群管理员通过 Console 页面管理各集群的 Node，也支持集群管理员直接使用黑屏操作对 各集群 Node 进行管理

## 查看节点信息

点击 Node 名称来查看节点的具体信息，点击【更多】查看 Node 的所有标签，

![node-info](/imgs/user-guide/administration/k8s-cluster/cluster-scoped-res/Node/node-info.png)

## 添加节点

KubeCube 支持集群管理员通过黑屏操作来自行添加节点，也可以点击【添加节点】来使用 KubeCube 的脚本来进行节点添加

// todo：确认 master 和 node 节点的添加细节

![add-node](/imgs/user-guide/administration/k8s-cluster/cluster-scoped-res/Node/add-node.png)

## 节点操作

点击【编辑标签】来对节点的标签进行编辑

![edit-labels](/imgs/user-guide/administration/k8s-cluster/cluster-scoped-res/Node/edit-node.png)

点击【禁止调度】来限制 pod 调度到该节点

![no-schedual](/imgs/user-guide/administration/k8s-cluster/cluster-scoped-res/Node/no-schedual.png)

点击【更多】来对节点进行更多高级操作，包括：设置节点类型、设置污点、平滑迁移等

![edit-node](/imgs/user-guide/administration/k8s-cluster/cluster-scoped-res/Node/edit-node.png)
