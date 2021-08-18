---
title: "添加节点"
weight: 5
---

KubeCube 提供为已有集群添加节点的能力

## 向集群添加工作节点

在新节点上执行部署脚本

```bash
KUBECUBE_VERSION=v1.0.0
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

tip: 你可以通过预先下载离线包和镜像来减少部署时间
```bash
export PRE_DOWNLOAD="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待新节点加入集群

- MASTER_IP 为 master 节点 ip
- LOCAL_IP 为新节点 ip

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="false"

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# if install k8s
INSTALL_KUBERNETES="true"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="node-join-master"

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME=""

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="" #{ip}:{port} , dns

# master ip means master node ip of cluster
MASTER_IP="10.173.32.4"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

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

## 向集群的 control-plane 添加 master 节点

在新节点上执行部署脚本

```bash
KUBECUBE_VERSION=v1.0.0
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待新节点加入 control-plane

-  MASTER_IP 需要填已有的 master 节点 ip
-  LOCAL_IP 需要填新节点的节点 ip
-  CONTROL_PLANE_ENDPOINT 为高可用 vip

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="false"

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# if install k8s
INSTALL_KUBERNETES="true"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="node-join-control-plane"

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME=""

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="10.173.32.10" #{ip}:{port} , dns

# master ip means master node ip of cluster
MASTER_IP="10.173.32.4"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

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