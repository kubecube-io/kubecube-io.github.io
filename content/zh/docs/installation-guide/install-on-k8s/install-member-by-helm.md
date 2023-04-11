---
title: "通过helm纳管计算集群"
weight: 9
---

使用 helm 的方式在计算集群中安装 Warden（KubeCube 的计算集群 agent），安装完成后，Warden 将主动向管控集群的 KubeCube 注册计算集群信息，完成计算集群的纳管。

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

### 通过 helm 在计算集群上安装 Warden

创建 member-value.yaml 文件并填写必要的 value 值

```yaml
# member-value.yaml

global:
  # 管控集群的 Node IP，用来暴露 KubeCube 的 NodePort service
  nodeIP: x.x.x.x

  dependencesEnable:
    ingressController: "false" # 如果集群中没有部署 ingress controller，请将此设置为 true
    localPathStorage: "false" # 如果集群中没有部署 local path storage，请将此设置为 true
    metricServer: "false" # # 如果集群中没有部署 metric server，请将此设置为 true

  # 以下为计算集群指定安装组件，请不要修改
  componentsEnable:
    kubecube: "false"
    warden: "true"
    audit: "false"
    webconsole: "false"
    cloudshell: "false"
    frontend: "false"
    
  # 如果要启动日志功能，请假以下值设置为 "enabled"
  hotPlugEnable:
    common:
      logagent: "disabled"

  localKubeConfig: xx # 当前集群的 kubeconfig 的 base64
  pivotKubeConfig: xx # 管控集群的 kubeconfig 的 base64

warden:
  containers:
    warden:
      args:
        inMemberCluster: true
        cluster: "member-cluster"  # 集群集群名
```

部署 Warden

`helm install warden -n kubecube-system --create-namespace ./kubecube-chart -f ./pivot-value.yaml`

### 卸载计算集群中的 warden

注意：`not found` 错误可以被忽略

1. 在管控面删除 cluster cr
```bash
kubectl delete cluster {计算集群名} 
```

2. 在计算集群手动清理 webhook
```bash
kubectl delete validatingwebhookconfigurations kubecube-validating-webhook-configuration warden-validating-webhook-configuration kubecube-monitoring-admission
```

3. 在计算集群卸载 warden chart
```bash
helm uninstall warden -n kubecube-system
```

4. 再计算集群清理残留资源
```bash
kubectl delete sa/kubecube-pre-job -n kubecube-system
kubectl delete clusterRole/kubecube-pre-job 
kubectl delete clusterRoleBinding/kubecube-pre-job
kubectl delete ns kubecube-system hnc-system kubecube-monitoring
```
