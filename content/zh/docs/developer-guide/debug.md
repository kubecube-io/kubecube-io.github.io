---
title: "如何调试"
weight: 4
---

## 如何进行本地调试

**安装 docker**

参照 [official docker installation guide](https://docs.docker.com/install/).

**安装 minikube**

参照 [official minikube installation guide](https://kubernetes.io/docs/tasks/tools/install-minikube/).

**使用 kubeadm 创建集群**

参照  [official creating a cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

### 本地调试

安装 manifests 依赖

⚠️ K8S_API_SERVER_ENDPOINT 为 k8s 的 api-server 地址，请根据环境填入对应的地址

```bash
K8S_API_SERVER_ENDPOINT="0.0.0.0:6443"
bash hack/install_local.sh ${K8S_API_SERVER_ENDPOINT}
```

调试 kubecube

```bash
make run-cube
```

调试 warden

```bash
make run-warden
```

卸载 manifests 依赖

```bash
bash hack/uninstall_local.sh
```

### 使用 pod 进行调试

请参照 [all in one 部署](https://www.kubecube.io/docs/quick-start/installation/#all-in-one-部署) 进行 kubecube 安装

构建 kubecube 镜像

```bash
make docker-build-cube IMG={image-tag}
```

构建 warden 镜像

```
make docker-build-wardeb IMG={image-tag}
```

使用构建完成的镜像替换对应 deployment 中的镜像











