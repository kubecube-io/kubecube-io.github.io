---
title: "多节点高可用部署"
weight: 6
---

本文提供 Kubernetes 的高可用部署和 KubeCube 的高可用部署方案，VIP 的实现需要用户自行提供

## v1.4.x

### 主机规划

| IP 地址      | 主机名  | 角色                 |
| :----------- | :------ | :------------------- |
| 10.173.32.2  | lb1     | Keepalived & HAproxy |
| 10.173.32.3  | lb2     | Keepalived & HAproxy |
| 10.173.32.4  | master1 | master, etcd         |
| 10.173.32.5  | master2 | master, etcd         |
| 10.173.32.6  | master3 | master, etcd         |
| 10.173.32.7  | worker1 | worker               |
| 10.173.32.8  | worker2 | worker               |
| 10.173.32.9  | worker3 | worker               |
| 10.173.32.10 |         | vip 地址             |

> ⚠️master2、master3、worker1、worker2、worker3 需要能够通过密钥或者密码 ssh 访问 master1

### 部署高可用 Kubernetes

KubeCube 部署脚本提供部署高可用 k8s 的能力，当然，你也可以使用其他工具搭建高可用的 k8s 集群

#### 开始安装

在 master1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.4
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 Kubernetes 安装完成，master2 和 master3 加入 control-plane 的方式与之相同

- CONTROL_PLANE_ENDPOINT 为高可用 k8s-apiserver 的 vip，在此我们用任意 master 节点的 ip 代替

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="false"

# if install k8s
INSTALL_KUBERNETES="true"

# k8s cni, support now is calico only
CNI="calico"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="control-plane-master"

# zone has two choice
# 1. "cn" : in mainland
# 2. "others" : out of mainland
ZONE="cn"

# k8s version you want to install
# support now is: 1.19.13, 1.20.9, 1.21.2, 1.22.2, 1.23.5
KUBERNETES_VERSION="1.23.5"

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="10.173.32.4" #{ip}:{port} , dns

# +optional
# KUBERNETES_BIND_ADDRESS generally is node_ip
# can be set when NODE_MODE="master" ot "control-plane-master"
# default value is $(hostname -I |awk '{print $1}')
KUBERNETES_BIND_ADDRESS="" #{node_ip}

#######################################################################
# member cluster config
# used when INSTALL_KUBECUBE_MEMBER="true"
#######################################################################

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME=""

#######################################################################
# ssh config
# used when NODE_MODE="node-join-master" or node-join-control-plane
#######################################################################

# +optional
# master ip means master node ip of cluster
MASTER_IP=""

# +optional
# the user who can access master node, it can be empty
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"

#######################################################################
# offline config
# used when offline install choose, must lift offline pkg first
#######################################################################

OFFLINE_INSTALL="false"

OFFLINE_PKG_PATH=""

#######################################################################
# container runtime config
# if value is docker, then use docker as container runtime
# else if value is containerd, then use containerd as container runtime
#######################################################################
CONTAINER_RUNTIME="containerd"
```

#### worker1 作为工作节点加入集群

在 worker1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.4
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 worker1 加入集群，worker2 和 worker3 加入集群的方式与之相同

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="false"

# if install k8s
INSTALL_KUBERNETES="true"

# k8s cni, support now is calico only
CNI="calico"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="node-join-master"

# zone has two choice
# 1. "cn" : in mainland
# 2. "others" : out of mainland
ZONE="cn"

# k8s version you want to install
# support now is: 1.19.13, 1.20.9, 1.21.2, 1.22.2, 1.23.5
KUBERNETES_VERSION="1.23.5"

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="" #{ip}:{port} , dns

# +optional
# KUBERNETES_BIND_ADDRESS generally is node_ip
# can be set when NODE_MODE="master" ot "control-plane-master"
# default value is $(hostname -I |awk '{print $1}')
KUBERNETES_BIND_ADDRESS="" #{node_ip}

#######################################################################
# member cluster config
# used when INSTALL_KUBECUBE_MEMBER="true"
#######################################################################

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME=""

#######################################################################
# ssh config
# used when NODE_MODE="node-join-master" or node-join-control-plane
#######################################################################

# +optional
# master ip means master node ip of cluster
MASTER_IP=""

# +optional
# the user who can access master node, it can be empty
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"

#######################################################################
# offline config
# used when offline install choose, must lift offline pkg first
#######################################################################

OFFLINE_INSTALL="false"

OFFLINE_PKG_PATH=""

#######################################################################
# container runtime config
# if value is docker, then use docker as container runtime
# else if value is containerd, then use containerd as container runtime
#######################################################################
CONTAINER_RUNTIME="docker"
```

### 部署高可用 KubeCube

在 master1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.4
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 KubeCube 部署完成

- install.conf

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="true"

# if install k8s
INSTALL_KUBERNETES="false"

# k8s cni, support now is calico only
CNI="calico"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="master"

# zone has two choice
# 1. "cn" : in mainland
# 2. "others" : out of mainland
ZONE="cn"

# k8s version you want to install
# support now is: 1.19.13, 1.20.9, 1.21.2, 1.22.2, 1.23.5
KUBERNETES_VERSION="1.23.5"

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="" #{ip}:{port} , dns

# +optional
# KUBERNETES_BIND_ADDRESS generally is node_ip
# can be set when NODE_MODE="master" ot "control-plane-master"
# default value is $(hostname -I |awk '{print $1}')
KUBERNETES_BIND_ADDRESS="" #{node_ip}

#######################################################################
# member cluster config
# used when INSTALL_KUBECUBE_MEMBER="true"
#######################################################################

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME=""

#######################################################################
# ssh config
# used when NODE_MODE="node-join-master" or node-join-control-plane
#######################################################################

# +optional
# master ip means master node ip of cluster
MASTER_IP=""

# +optional
# the user who can access master node, it can be empty
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"

#######################################################################
# offline config
# used when offline install choose, must lift offline pkg first
#######################################################################

OFFLINE_INSTALL="false"

OFFLINE_PKG_PATH=""

#######################################################################
# container runtime config
# if value is docker, then use docker as container runtime
# else if value is containerd, then use containerd as container runtime
#######################################################################
CONTAINER_RUNTIME="docker"
```

## v1.2.x

### 主机规划

| IP 地址      | 主机名  | 角色                 |
| :----------- | :------ | :------------------- |
| 10.173.32.2  | lb1     | Keepalived & HAproxy |
| 10.173.32.3  | lb2     | Keepalived & HAproxy |
| 10.173.32.4  | master1 | master, etcd         |
| 10.173.32.5  | master2 | master, etcd         |
| 10.173.32.6  | master3 | master, etcd         |
| 10.173.32.7  | worker1 | worker               |
| 10.173.32.8  | worker2 | worker               |
| 10.173.32.9  | worker3 | worker               |
| 10.173.32.10 |         | vip 地址             |

> ⚠️master2、master3、worker1、worker2、worker3 需要能够通过密钥或者密码 ssh 访问 master1

### 部署高可用 Kubernetes

KubeCube 部署脚本提供部署高可用 k8s 的能力，当然，你也可以使用其他工具搭建高可用的 k8s 集群

#### 开始安装

在 master1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.2
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 Kubernetes 安装完成，master2 和 master3 加入 control-plane 的方式与之相同

- CONTROL_PLANE_ENDPOINT 为高可用 k8s-apiserver 的 vip，在此我们用任意 master 节点的 ip 代替

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="false"

# if install k8s
INSTALL_KUBERNETES="true"

# k8s cni, support now is calico only
CNI="calico"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="control-plane-master"

# zone has two choice
# 1. "cn" : in mainland
# 2. "others" : out of mainland
ZONE="cn"

# k8s version you want to install
# support now is: 1.19.13, 1.20.9, 1.21.2, 1.22.2, 1.23.5
KUBERNETES_VERSION="1.23.5"

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="10.173.32.4" #{ip}:{port} , dns

# +optional
# KUBERNETES_BIND_ADDRESS generally is node_ip
# can be set when NODE_MODE="master" ot "control-plane-master"
# default value is $(hostname -I |awk '{print $1}')
KUBERNETES_BIND_ADDRESS="" #{node_ip}

#######################################################################
# member cluster config
# used when INSTALL_KUBECUBE_MEMBER="true"
#######################################################################

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME=""

#######################################################################
# ssh config
# used when NODE_MODE="node-join-master" or node-join-control-plane
#######################################################################

# +optional
# master ip means master node ip of cluster
MASTER_IP=""

# +optional
# the user who can access master node, it can be empty
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"

#######################################################################
# offline config
# used when offline install choose, must lift offline pkg first
#######################################################################

OFFLINE_INSTALL="false"

OFFLINE_PKG_PATH=""

#######################################################################
# container runtime config
# if value is docker, then use docker as container runtime
# else if value is containerd, then use containerd as container runtime
#######################################################################
CONTAINER_RUNTIME="containerd"
```

#### worker1 作为工作节点加入集群

在 worker1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.2
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 worker1 加入集群，worker2 和 worker3 加入集群的方式与之相同

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="false"

# if install k8s
INSTALL_KUBERNETES="true"

# k8s cni, support now is calico only
CNI="calico"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="node-join-master"

# zone has two choice
# 1. "cn" : in mainland
# 2. "others" : out of mainland
ZONE="cn"

# k8s version you want to install
# support now is: 1.19.13, 1.20.9, 1.21.2, 1.22.2, 1.23.5
KUBERNETES_VERSION="1.23.5"

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="" #{ip}:{port} , dns

# +optional
# KUBERNETES_BIND_ADDRESS generally is node_ip
# can be set when NODE_MODE="master" ot "control-plane-master"
# default value is $(hostname -I |awk '{print $1}')
KUBERNETES_BIND_ADDRESS="" #{node_ip}

#######################################################################
# member cluster config
# used when INSTALL_KUBECUBE_MEMBER="true"
#######################################################################

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME=""

#######################################################################
# ssh config
# used when NODE_MODE="node-join-master" or node-join-control-plane
#######################################################################

# +optional
# master ip means master node ip of cluster
MASTER_IP=""

# +optional
# the user who can access master node, it can be empty
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"

#######################################################################
# offline config
# used when offline install choose, must lift offline pkg first
#######################################################################

OFFLINE_INSTALL="false"

OFFLINE_PKG_PATH=""

#######################################################################
# container runtime config
# if value is docker, then use docker as container runtime
# else if value is containerd, then use containerd as container runtime
#######################################################################
CONTAINER_RUNTIME="containerd"
```

### 部署高可用 KubeCube

在 master1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.2
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 KubeCube 部署完成

- install.conf

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="true"

# if install k8s
INSTALL_KUBERNETES="false"

# k8s cni, support now is calico only
CNI="calico"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="master"

# zone has two choice
# 1. "cn" : in mainland
# 2. "others" : out of mainland
ZONE="cn"

# k8s version you want to install
# support now is: 1.19.13, 1.20.9, 1.21.2, 1.22.2, 1.23.5
KUBERNETES_VERSION="1.23.5"

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="" #{ip}:{port} , dns

# +optional
# KUBERNETES_BIND_ADDRESS generally is node_ip
# can be set when NODE_MODE="master" ot "control-plane-master"
# default value is $(hostname -I |awk '{print $1}')
KUBERNETES_BIND_ADDRESS="" #{node_ip}

#######################################################################
# member cluster config
# used when INSTALL_KUBECUBE_MEMBER="true"
#######################################################################

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME=""

#######################################################################
# ssh config
# used when NODE_MODE="node-join-master" or node-join-control-plane
#######################################################################

# +optional
# master ip means master node ip of cluster
MASTER_IP=""

# +optional
# the user who can access master node, it can be empty
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"

#######################################################################
# offline config
# used when offline install choose, must lift offline pkg first
#######################################################################

OFFLINE_INSTALL="false"

OFFLINE_PKG_PATH=""

#######################################################################
# container runtime config
# if value is docker, then use docker as container runtime
# else if value is containerd, then use containerd as container runtime
#######################################################################
CONTAINER_RUNTIME="containerd"
```

## v1.1.x

### 主机规划

| IP 地址      | 主机名  | 角色                 |
| :----------- | :------ | :------------------- |
| 10.173.32.2  | lb1     | Keepalived & HAproxy |
| 10.173.32.3  | lb2     | Keepalived & HAproxy |
| 10.173.32.4  | master1 | master, etcd         |
| 10.173.32.5  | master2 | master, etcd         |
| 10.173.32.6  | master3 | master, etcd         |
| 10.173.32.7  | worker1 | worker               |
| 10.173.32.8  | worker2 | worker               |
| 10.173.32.9  | worker3 | worker               |
| 10.173.32.10 |         | vip 地址             |

> ⚠️master2、master3、worker1、worker2、worker3 需要能够通过密钥或者密码 ssh 访问 master1

### 部署高可用 Kubernetes

KubeCube 部署脚本提供部署高可用 k8s 的能力，当然，你也可以使用其他工具搭建高可用的 k8s 集群

#### 开始安装

在 master1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.1
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 Kubernetes 安装完成，master2 和 master3 加入 control-plane 的方式与之相同

- CONTROL_PLANE_ENDPOINT 为高可用 k8s-apiserver 的 vip，在此我们用任意 master 节点的 ip 代替

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="false"

# if install k8s
INSTALL_KUBERNETES="true"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="control-plane-master"

# zone has two choice
# 1. "cn" : in mainland
# 2. "others" : out of mainland
ZONE="cn"

# k8s version you want to install
# support now is: 1.20.9, 1.19.13, 1.18.20, 1.21.2
KUBERNETES_VERSION="1.20.9"

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="10.173.32.4" #{ip}:{port} , dns

#######################################################################
# member cluster config
# used when INSTALL_KUBECUBE_MEMBER="true"
#######################################################################

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME=""

#######################################################################
# ssh config
# used when NODE_MODE="node-join-master" or node-join-control-plane
#######################################################################

# +optional
# master ip means master node ip of cluster
MASTER_IP=""

# +optional
# the user who can access master node, it can be empty
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"

#######################################################################
# offline config
# used when offline install choose, must lift offline pkg first
#######################################################################

OFFLINE_INSTALL="false"

OFFLINE_PKG_PATH=""
```

#### worker1 作为工作节点加入集群

在 worker1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.1
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 worker1 加入集群，worker2 和 worker3 加入集群的方式与之相同

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="false"

# if install k8s
INSTALL_KUBERNETES="true"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="node-join-master"

# zone has two choice
# 1. "cn" : in mainland
# 2. "others" : out of mainland
ZONE="cn"

# k8s version you want to install
# support now is: 1.20.9, 1.19.13, 1.18.20, 1.21.2
KUBERNETES_VERSION="1.20.9"

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="" #{ip}:{port} , dns

#######################################################################
# member cluster config
# used when INSTALL_KUBECUBE_MEMBER="true"
#######################################################################

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME=""

#######################################################################
# ssh config
# used when NODE_MODE="node-join-master" or node-join-control-plane
#######################################################################

# +optional
# master ip means master node ip of cluster
MASTER_IP="10.173.32.4"

# +optional
# the user who can access master node, it can be empty
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"

#######################################################################
# offline config
# used when offline install choose, must lift offline pkg first
#######################################################################

OFFLINE_INSTALL="false"

OFFLINE_PKG_PATH=""
```

### 部署高可用 KubeCube

在 master1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.1
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 KubeCube 部署完成

- install.conf

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="true"

# if install k8s
INSTALL_KUBERNETES="false"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="control-plane-master"

# zone has two choice
# 1. "cn" : in mainland
# 2. "others" : out of mainland
ZONE="cn"

# k8s version you want to install
# support now is: 1.20.9, 1.19.13, 1.18.20, 1.21.2
KUBERNETES_VERSION="1.20.9"

# +optional
# must be set when NODE_MODE="control-plane-master"
# or "node-join-control-plane"
CONTROL_PLANE_ENDPOINT="" #{ip}:{port} , dns

#######################################################################
# member cluster config
# used when INSTALL_KUBECUBE_MEMBER="true"
#######################################################################

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME=""

#######################################################################
# ssh config
# used when NODE_MODE="node-join-master" or node-join-control-plane
#######################################################################

# +optional
# master ip means master node ip of cluster
MASTER_IP="10.173.32.4"

# +optional
# the user who can access master node, it can be empty
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"

#######################################################################
# offline config
# used when offline install choose, must lift offline pkg first
#######################################################################

OFFLINE_INSTALL="false"

OFFLINE_PKG_PATH=""
```

- cube.conf

将`kubecube_replicas`设置为3，使得 KubeCube 使用 3 副本部署，并且由于`podAntiAffinity`

```bash
# custom values for kubecube

kubecube_replicas=3
kubecube_args_logLevel="info"
```

## v1.0.x

### 主机规划

| IP 地址      | 主机名  | 角色                 |
| :----------- | :------ | :------------------- |
| 10.173.32.2  | lb1     | Keepalived & HAproxy |
| 10.173.32.3  | lb2     | Keepalived & HAproxy |
| 10.173.32.4  | master1 | master, etcd         |
| 10.173.32.5  | master2 | master, etcd         |
| 10.173.32.6  | master3 | master, etcd         |
| 10.173.32.7  | worker1 | worker               |
| 10.173.32.8  | worker2 | worker               |
| 10.173.32.9  | worker3 | worker               |
| 10.173.32.10 |         | vip 地址             |

> ⚠️master2、master3、worker1、worker2、worker3 需要能够通过密钥或者密码 ssh 访问 master1

### 部署高可用 Kubernetes

#### 开始安装

在 master1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.0
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 Kubernetes 安装完成

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
NODE_MODE="control-plane-master"

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
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
# when NODE_MODE="master" or "control-plane-master"
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"
```

#### master2 节点加入 control-plane

在 master2 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.0
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 master2 加入 control-plane

> master3 加入 control-plane 与此类似，仅需修改 `LOCAL_IP`为`10.173.32.6`

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
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
# when NODE_MODE="master" or "control-plane-master"
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"
```

#### worker1 作为工作节点加入集群

在 worker1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.0
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 worker1 加入集群

> worker2 和 worker3 加入集群的方式与之类似，仅需修改`LOCAL_IP`为本机 IP 即可

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
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
# when NODE_MODE="master" or "control-plane-master"
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"
```

### 部署高可用 KubeCube

在 master1 上执行部署脚本

```bash
KUBECUBE_VERSION=v1.0
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

设置脚本参数，并按照提示继续运行安装脚本并等待 KubeCube 部署完成

- install.conf

```bash
# if install kubecube on pivot cluster
INSTALL_KUBECUBE_PIVOT="true"

# if install kubecube on member cluster
INSTALL_KUBECUBE_MEMBER="false"

# if install k8s
INSTALL_KUBERNETES="false"

# there are four node mode below:
# "master" : node will be installed as a master of cluster
# "node-join-master" : node will be install as a worker of cluster to join master
# "control-plane-master" : node will be installed as a master to control plane of cluster
# "node-join-control-plane" : node will be installed as a master to join control plane
NODE_MODE="control-plane-master"

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
SSH_USER="root"

# +optional
# the port specified to access master node, it can be empty
# when NODE_MODE="master" or "control-plane-master"
SSH_PORT=22

# +optional
# must be empty when ACCESS_PRIVATE_KEY_PATH set
# password for master user to access master node
ACCESS_PASSWORD=""

# +optional
# must be empty when ACCESS_PASSWORD set
# ACCESS_PRIVATE_KEY for master user to access master node
ACCESS_PRIVATE_KEY_PATH="/root/.ssh/id_rsa"
```

- cube.conf

将`kubecube_replicas`设置为3，使得 KubeCube 使用 3 副本部署，并且由于`podAntiAffinity`，它们会运行在非`controlPlane`的节点上，并且每个节点仅运行单个副本

```bash
# custom values for kubecube

kubecube_replicas=3
kubecube_args_logLevel="info"
```

