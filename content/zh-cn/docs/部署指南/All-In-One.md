---
title: "All In One 最小化部署"
weight: 2
---

对于想要快速开始、快速体验的用户来说，All In One 是最佳的安装方式

# 在 Linux 上部署 KubeCube

## 开始安装

在 Linux 机器上执行部署脚本

```bash
curl -fsSL https://gitee.com/kubecube/manifests/raw/master/entry.sh | bash
```

## 等待部署完成

// todo: img

## 使用 admin 账户登陆 console


# 在 Kubernetes 集群中部署 KubeCube

## ⚠️修改 Kubernetes API-Server 配置

**必要性**

1. KubeCube 对多集群提供统一的认证和鉴权服务，需要使用 k8s api-server 的 auth-webhook 能力来做拓展。

2. KubeCube 提供对 k8s-apiserver 日志进行审计的能力，这需要为 k8s api-server 指定审计服务后端。

**修改操作**

如果您的 k8s api-server 服务是以 deployment 形式运行的，请直接修改 deployment ；如果您的 k8s api-server 服务是以 static pod 形式运行的，您需要修改对应的 manifest 文件，它的文件路径通常为 `/etc/kubernetes/manifests/kube-apiserver.yaml    ` ，修改内容如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
  ...
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
      ...
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
         ...
      name: kube-apiserver
      volumeMounts:
        - mountPath: /var/log/audit
          name: audit-log
        - mountPath: /etc/cube
          name: cube
          readOnly: true
        ...
  ...
  volumes:
    - hostPath:
        path: /var/log/audit
        type: DirectoryOrCreate
      name: audit-log
    - hostPath:
        path: /etc/cube
        type: DirectoryOrCreate
      name: cube
  ...
```

## 开始安装

在 Linux 机器上执行部署脚本

```bash
export CUSTOMIZE="true";curl -fsSL https://gitee.com/kubecube/manifests/raw/master/entry.sh | bash
```

## 设置安装脚本参数

// todo: img

## 等待部署完成



## 使用 admin 账户登陆 console

