---
title: "添加计算集群"
weight: 4
---

KubeCube 可以添加其它集群作为计算集群，前提是，计算集群能够访问管控集群的 k8s api-server 和 KubeCube，默认情况下 KubeCube 使用 NodePort 对外暴露服务，用户可自行使用 ingress 进行暴露

## v1.4.x

### 方式一：部署新集群并添加

在 linux 机器上，需要构建 Kubernetes 集群并安装 KubeCube 依赖项

#### 开始安装

```bash
KUBECUBE_VERSION=v1.4
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

#### 设置安装脚本参数

该安装模式下，需要修改以下参数：

INSTALL_KUBECUBE_PIVOT="false"

INSTALL_KUBECUBE_MEMBER="true"

INSTALL_KUBERNETES="true"

MEMBER_CLUSTER_NAME="member-1"

MASTER_IP="${node ip}"

KUBECUBE_HOST="${pivot node ip}"

> MEMBER_CLUSTER_NAME 表示计算集群的名字，注意，不能与已有的计算集群名称同名,
> ${node ip} 表示你运行脚本所在 node 机器的 ip，该 node 需要可操作 kubectl,
> ${pivot node ip} 表示管控集群 node 机器的 ip，用于向 KubeCube 注册集群

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
INSTALL_KUBECUBE_MEMBER="true"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST="y.y.y.y"

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME="member-1"

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

### 方式二：纳管已有集群

添加已有集群只需从 console 页面导入集群信息即可

#### 在 console 页面中导入集群信息

![add-member-cluster](/imgs/installation-guide/add-member-k8s/add-member-cluster.png)

### 在 console 中确认新集群

![check-cluster](/imgs/installation-guide/add-member-k8s/check-cluster.png)

## v1.2.x

### 方式一：部署新集群并添加

在 linux 机器上，需要构建 Kubernetes 集群并安装 KubeCube 依赖项

#### 开始安装

```bash
KUBECUBE_VERSION=v1.2
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

#### 设置安装脚本参数

该安装模式下，需要修改以下参数：

INSTALL_KUBECUBE_PIVOT="false"

INSTALL_KUBECUBE_MEMBER="true"

INSTALL_KUBERNETES="true"

MEMBER_CLUSTER_NAME="member-1"

MASTER_IP="${node ip}"

KUBECUBE_HOST="${pivot node ip}"

> MEMBER_CLUSTER_NAME 表示计算集群的名字，注意，不能与已有的计算集群名称同名,
> ${node ip} 表示你运行脚本所在 node 机器的 ip，该 node 需要可操作 kubectl,
> ${pivot node ip} 表示管控集群 node 机器的 ip，用于向 KubeCube 注册集群

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
INSTALL_KUBECUBE_MEMBER="true"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST="y.y.y.y"

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME="member-1"

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

### 方式二：纳管已有集群

添加已有集群只需从 console 页面导入集群信息即可

#### 在 console 页面中导入集群信息

![add-member-cluster](/imgs/installation-guide/add-member-k8s/add-member-cluster.png)

### 在 console 中确认新集群

![check-cluster](/imgs/installation-guide/add-member-k8s/check-cluster.png)

## v1.1.x

### 方式一：部署新集群并添加

在 linux 机器上，需要构建 Kubernetes 集群并安装 KubeCube 依赖项

#### 开始安装

```bash
KUBECUBE_VERSION=v1.1
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

#### 设置安装脚本参数

该安装模式下，需要修改以下参数：

INSTALL_KUBECUBE_PIVOT="false"

INSTALL_KUBECUBE_MEMBER="true"

INSTALL_KUBERNETES="true"

MEMBER_CLUSTER_NAME="member-1"

MASTER_IP="${node ip}"

KUBECUBE_HOST="${pivot node ip}"

> MEMBER_CLUSTER_NAME 表示计算集群的名字，注意，不能与已有的计算集群名称同名,
> ${node ip} 表示你运行脚本所在 node 机器的 ip，该 node 需要可操作 kubectl,
> ${pivot node ip} 表示管控集群 node 机器的 ip，用于向 KubeCube 注册集群

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
NODE_MODE="master"

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
INSTALL_KUBECUBE_MEMBER="true"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST="y.y.y.y"

# +optional
# must be set when INSTALL_KUBECUBE_MEMBER="true"
# this value is the name of member cluster you
# want to take over
MEMBER_CLUSTER_NAME="member-1"

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

### 方式二：纳管已有集群

添加已有集群只需从 console 页面导入集群信息即可

#### 在 console 页面中导入集群信息

![add-member-cluster](/imgs/installation-guide/add-member-k8s/add-member-cluster.png)

### 在 console 中确认新集群

![check-cluster](/imgs/installation-guide/add-member-k8s/check-cluster.png)

## v1.0.x

### 方式一：部署新集群并添加

在 linux 机器上，需要构建 Kubernetes 集群并安装 KubeCube 依赖项

#### 开始安装

```bash
KUBECUBE_VERSION=v1.0
```
```bash
export CUSTOMIZE="true";curl -fsSL https://kubecube.nos-eastchina1.126.net/kubecube-installer/${KUBECUBE_VERSION}/entry.sh | bash
```

#### 设置安装脚本参数

该安装模式下，需要修改以下参数：

INSTALL_KUBECUBE_PIVOT="false"

INSTALL_KUBECUBE_MEMBER="true"

INSTALL_KUBERNETES="true"

MEMBER_CLUSTER_NAME="member-1"

MASTER_IP="${node ip}"

KUBECUBE_HOST="${pivot node ip}"

> MEMBER_CLUSTER_NAME 表示计算集群的名字，注意，不能与已有的计算集群名称同名
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

### 方式二：纳管已有集群

添加已有集群需要从 console 页面获取添加集群的定制脚本

#### 在 console 页面中获取添加集群的脚本

![add-member-cluster](/imgs/installation-guide/add-member-k8s/add-member-cluster.png)

#### 使用脚本添加集群
在集群的 node 机器上，使用从 console 中下载的脚本，该机器需要能够执行 kubectl

```bash
/bin/bash add_cluster.sh
```

### 等待集群添加完成

![add-cluster](/imgs/installation-guide/add-member-k8s/add-cluster.png)

### 在 console 中确认新集群

![check-cluster](/imgs/installation-guide/add-member-k8s/check-cluster.png)