---
title: "通过 hotplug 启用 loggie"
weight: 9
---

KubeCube 使用 hotplug 热插拔方式集成 Loggie，用户可以通过打开 hotplug 中关于日志部分的开关来开启 Loggie。我们有两个时间点可以用来开启 Loggie。

## 在使用 Helm 安装 KubeCube 时

在使用 Helm 安装 KubeCube 时，我们可以设置以下 values 来开启 Loggie。

安装管控集群时的参数如下：

```yaml
# pivot-value.yaml
...
global:
  # set "enabled" if wanna open log application.
  hotPlugEnable:
    pivot:
      logseer: "enabled" 
      logagent: "enabled"
      elasticsearch: "enabled"
...
```

安装计算集群时的参数如下：

```yaml
# member-value.yaml
...
global:    
  # set "enabled" if wanna open log application.
  hotPlugEnable:
    common:
      logagent: "enabled"
...
```

## 在使用过程中

在使用过程中，我们也可以通过直接修改 hotplug 的方式来开启 Loggie。

修改管控集群的 hotplug 如下：

```bash
kubectl edit hotplug pivot-cluster

# pivot-cluster
apiVersion: hotplug.kubecube.io/v1
kind: Hotplug
metadata:
  name: pivot-cluster
spec:
  component:
  - name: elasticsearch
    namespace: elasticsearch
    pkgName: elasticsearch-7.8.1.tgz
    status: enabled # 将该值设为 enabled 来开启日志
...
  - name: logseer
    status: enabled # 将该值设为 enabled 来开启日志
  - name: logagent
    status: enabled # 将该值设为 enabled 来开启日志
```

修改计算集群的 hotplug 如下：

```bash
kubectl edit hotplug common

# common
apiVersion: hotplug.kubecube.io/v1
kind: Hotplug
metadata:
  name: common
spec:
  component:
...
  - env: |
      clustername: "{{.cluster}}"
      elasticsearch:
        address: x.x.x.x:32200 # 填写管控集群的 es 的 nodeport svc 访问地址，一般为 {nodeIP}:32200
    name: logagent
    namespace: logagent
    pkgName: logagent-1.3.0.tgz
    status: enabled # 将该值设为 enabled 来开启日志
```