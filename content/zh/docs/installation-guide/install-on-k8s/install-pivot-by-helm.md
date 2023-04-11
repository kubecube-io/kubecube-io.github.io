---
title: "通过helm安装KubeCube"
weight: 8
---

通过 helm 的方式在管控集群中部署 KubeCube

## v1.8.x

### 要求
1. 已有 K8s 集群环境
2. helm v3 客户端

### 下载 KubeCube helm chart 包

```bash
KUBECUBE_VERSION=v1.8
```

```bash
curl -s https://kubecube.nos-eastchina1.126.net/kubecube-chart/${KUBECUBE_VERSION}/kubecube-chart.tar.gz | tar -xz
```

### 通过 helm 在管控集群上安装 KubeCube

创建 pivot-value.yaml 文件并填写必要的 value 值

```yaml
# pivot-value.yaml

global:
  # 管控集群的 Node IP，用来暴露 KubeCube 的 NodePort service
  nodeIP: x.x.x.x

  dependencesEnable:
    ingressController: "false" # 如果集群中没有部署 ingress controller，请将此设置为 "true"
    localPathStorage: "false" # 如果集群中没有部署 local path storage，请将此设置为 "true"
    metricServer: "false" # # 如果集群中没有部署 metric server，请将此设置为 "true"
    
  # 如果要启动日志功能，请假以下值设置为 "enabled"
  hotPlugEnable:
    pivot:
      logseer: "disabled" 
      logagent: "disabled"
      elasticsearch: "disabled"    

  localKubeConfig: xx # 管控集群的 kubeconfig 的 base64
  pivotKubeConfig: xx # 管控集群的 kubeconfig 的 base64

warden:
  containers:
    warden:
      args:
        cluster: "pivot-cluster"  # 管控集群名
```

部署 KubeCube

`helm install kubecube -n kubecube-system --create-namespace ./kubecube-chart -f ./pivot-value.yaml`

### 卸载管控集群中的 KubeCube

注意：`not found` 错误可以被忽略

1. 手动清理 webhook
```bash
kubectl delete validatingwebhookconfigurations kubecube-validating-webhook-configuration warden-validating-webhook-configuration kubecube-monitoring-admission
```

2. 卸载 KubeCube chart
```bash
helm uninstall kubecube -n kubecube-system
```

3. 清理残留资源
```bash
kubectl delete sa/kubecube-pre-job -n kubecube-system
kubectl delete clusterRole/kubecube-pre-job 
kubectl delete clusterRoleBinding/kubecube-pre-job
kubectl delete ns kubecube-system hnc-system kubecube-monitoring
```
