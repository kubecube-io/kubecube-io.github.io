---
title: "All In One 最小化部署"
weight: 2
---

对于想要快速开始、快速体验的用户来说，All In One 是最佳的安装方式

## 在 Linux 上部署 KubeCube

### 开始安装

在 Linux 机器上执行部署脚本

```bash
curl -fsSL https://gitee.com/kubecube/manifests/raw/master/entry.sh | bash
```

## 在 Kubernetes 集群中部署 KubeCube

### ⚠️修改 Kubernetes API-Server 配置

**必要性**

1. KubeCube 对多集群提供统一的认证和鉴权服务，需要使用 k8s api-server 的 auth-webhook 能力来做拓展。

2. KubeCube 提供对 k8s-apiserver 日志进行审计的能力，这需要为 k8s api-server 指定审计服务后端。

**修改操作**

如果您的 k8s api-server 服务是以 deployment 形式运行的，请直接修改 deployment ；如果您的 k8s api-server 服务是以 static pod 形式运行的，您需要修改对应的 manifest 文件，它的文件路径通常为 `/etc/kubernetes/manifests/kube-apiserver.yaml    ` ，修改内容如下：

```
apiVersion: v1
kind: Pod
metadata:
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
    - command:
        - kube-apiserver
        - --audit-log-format=json
        - --audit-log-maxage=10
        - --audit-log-maxbackup=10
        - --audit-log-maxsize=100
        - --audit-log-path=/var/log/audit
        - --audit-policy-file=/etc/cube/audit/audit-policy.yaml
        - --audit-webhook-config-file=/etc/cube/audit/audit-webhook.config
        - --authentication-token-webhook-config-file=/etc/cube/warden/webhook.config
      name: kube-apiserver
      volumeMounts:
        - mountPath: /var/log/audit
          name: audit-log
        - mountPath: /etc/cube
          name: cube
          readOnly: true
  volumes:
    - hostPath:
        path: /var/log/audit
        type: DirectoryOrCreate
      name: audit-log
    - hostPath:
        path: /etc/cube
        type: DirectoryOrCreate
      name: cube
```

### 开始安装

在 Linux 机器上执行部署脚本

```bash
export CUSTOMIZE="true";curl -fsSL https://gitee.com/kubecube/manifests/raw/master/entry.sh | bash
```

### 设置安装脚本参数
该安装模式下，需要修改以下参数：

INSTALL_KUBECUBE_MEMBER="false"

MASTER_IP="${node ip}"

LOCAL_IP="${node ip}"
> ${node ip} 表示你运行脚本所在 node 机器的 ip，该 node 需要可操作 kubectl
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
NODE_MODE="master"

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
MASTER_IP="x.x.x.x"

# local ip mean node ip self, will equal to master ip
# when NODE_MODE="master" or "control-plane-master"
LOCAL_IP="x.x.x.x"

# +optional
# KUBECUBE_HOST must be set when as a member cluster to
# join pivot cluster, the value is pivot node ip
KUBECUBE_HOST=""

# zone has two choice
# 1. "cn" : in mainland
# 2. "others" : out of mainland
ZONE="cn"

# k8s version you want to install
KUBERNETES_VERSION="1.19.0"

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

## 等待部署完成
KubeCube 部署完成后，请根据提示信息登陆 console 管理页面

![complete-deploy](/imgs/部署指南/All-In-One/complete-deploy.png)

## 使用 admin 账户登陆 console

⚠️请在登陆后修改 admin 用户的密码

![login-console](/imgs/部署指南/All-In-One/login-console.png)
