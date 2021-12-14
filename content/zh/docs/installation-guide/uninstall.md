---
title: "KubeCube 卸载"
weight: 8
---

KubeCube 提供一键卸载脚本

⚠️ 注意，卸载脚本仅用于使用 KubeCube 部署脚本安装的环境，不应该用于任何正式环境

## 下载卸载脚本

```bash
curl -o cleanup.sh https://kubecube.nos-eastchina1.126.net/hack/cleanup.sh
```

## 一键卸载
一键卸载按顺序卸载 kubecube, kubernetes 和 docker
```bash
/bin/bash cleanup.sh all
```

## 指定卸载

卸载脚本可以指定卸载对象，但需注意卸载依赖

### 卸载 KubeCube
删除 kubecube chart release，并清理 kubecube 在本机上的相关文件
```bash
/bin/bash cleanup.sh kubecube
```

### 卸载 Kubernetes
卸载 kubernetes 集群，并清理 kubernetes 在本机上的相关文件
```bash
/bin/bash cleanup.sh k8s
```

### 卸载 Docker
卸载 docker，并清理 docker 在本机上的相关文件

⚠️注意：需在卸载 kubernetes 后才能卸载 docker，不然会造成 kubernetes 集群异常
```bash
/bin/bash cleanup.sh docker
```