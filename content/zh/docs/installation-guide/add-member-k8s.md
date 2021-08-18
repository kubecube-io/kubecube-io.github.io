---
title: "部署并添加计算集群"
weight: 4
---

KubeCube 可以添加其它集群作为计算集群，前提是，计算集群能够访问管控集群的 k8s api-server 和 KubeCube，默认情况下 KubeCube 使用 NodePort 对外暴露服务，用户可自行使用 ingress 进行暴露

## 在节点上构建新集群并添加

在新节点上，需要构建 Kubernetes 集群并安装 KubeCube 依赖项

### 开始安装

```bash
KUBECUBE_VERSION=v1.0.0
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

### 设置安装脚本参数

该安装模式下，需要修改以下参数：

INSTALL_KUBECUBE_PIVOT="false"

INSTALL_KUBECUBE_MEMBER="true"

INSTALL_KUBERNETES="true"

MEMBER_CLUSTER_NAME="member-1"

MASTER_IP="${node ip}"

KUBECUBE_HOST="${pivot node ip}"

> ${node ip} 表示你运行脚本所在 node 机器的 ip，该 node 需要可操作 kubectl
> ${pivot node ip} 表示管控集群 node 机器的 ip，用于向 KubeCube 注册集群

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="false"

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="true"

# if install k8s
INSTALL_KUBERNETES="true"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="master"

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME="member-1"

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="" #{ip}:{port} , dns

# master ip means master node ip of cluster
MASTER_IP="x.x.x.x"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST="y.y.y.y"

# zone has two choice
# 1. "cn" : in mainland
# 2. "others" : out of mainland
ZONE="cn"

# k8s version you want to install
KUBERNETES_VERSION="1.20.9"

# +optional
# the user who can access master node, it can be empty
# when NODE_MODE="master" or "control-plane-master"
MASTER_USER="root"

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"
```

## 添加已有集群

添加已有集群需要从 console 页面获取添加集群的定制脚本

### 在 console 页面中进行添加

todo

### 使用脚本添加集群
在集群的 node 机器上，使用从 console 中下载的脚本，该机器需要能够执行 kubectl

```bash
/bin/bash add_cluster.sh
```

## 等待集群添加完成

![add-cluster](/imgs/installation-guide/add-member-k8s/add-cluster.png)

## 在 console 中确认新集群

![check-cluster](/imgs/installation-guide/add-member-k8s/check-cluster.png)