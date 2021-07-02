---
title: "热插拔"
weight: 6
---

热插拔是通过修改配置文件实现不停机更新监控、日志、审计等组件的启停和配置。

## 热插拔

热插拔的实现是基于helm，因此集群需要预先安装好helm3版本。

登录管控 k8s 集群，执行命令可以查看热插拔配置

```bash
# kubectl get hotplug
NAME            PHASE     AGE
common          running   23h
pivot-cluster   running   16d
```

其中：

-  `common` 表示公共的热插拔配置，`pivot-cluster` 表示 pivot-cluster 这个 k8s 集群的热插拔配置。

- 允许自定义各个集群的热插拔配置覆盖 common 热插拔配置实现个性化配置 k8s 集群组件热插拔。

- 热插拔配置的 `.metadata.name` 要求与k8s集群名称一致，所有k8s集群信息查看命令为 `kubectl get cluster`。

## Common配置说明

目前，默认的Common配置包括以下几个组件：

`audit`：操作审计

`logseer`：日志管理组件，仅在管控集群安装

`logagent`：日志采集代理组件

`kubecube-monitoring`：监控prometheuse组件

`kubecube-thanos`：监控thanos组件，仅在管控集群安装

示例如下：

```yaml
apiVersion: hotplug.kubecube.io/v1
kind: Hotplug
metadata:
  annotations:
    kubecube.io/sync: "true"					## 同步信号，kubecube会将这个配置同步到各个集群
  name: common									## 公共配置为cmmon，其余集群特殊配置为集群名字
spec:
  component:
  - name: audit									## 审计日志
    status: disabled
  - name: logseer								## 日志管理组件
    namespace: logseer
    pkgName: logseer-v1.0.0.tgz
    status: disabled							## 启停标识：这里disabled为禁用
  - name: logagent								## 日志采集代理组件
    namespace: logagent
    pkgName: logagent-v1.0.0.tgz
    status: enabled								## 启停标识：这里enabled为启用
    env: |										## 环境变量
      clustername: "{{.cluster}}"
  - name: kubecube-monitoring					## 监控组件
    namespace: kubecube-monitoring
    pkgName: kubecube-monitoring-15.4.7.tgz
    status: enabled
    env: |
      grafana:
        enabled: false
      prometheus:
        prometheusSpec:
          externalLabels:
            cluster: "{{.cluster}}"
          remoteWrite:
          - url: http://10.173.32.42:31291/api/v1/receive
  - name: kubecube-thanos						## 监控thanos组件
    namespace: kubecube-monitoring
    pkgName: thanos-3.18.0.tgz
    status: disabled
status:                                         ## message显示各个组件运行状态，phase显示总体运行状态
  message: '{"kubecube-monitoring":"release is running","kubecube-thanos":"release    
    is running","logagent":"release is running","logseer":"release is running"}'
  phase: running

```

每一个组件基本包含5个要素，在 `spec.component` 下：

name：组件名称

namespace：指定组件部署的命名空间，若指定的命名空间不存在，会自动以该字段值去创建一个命名空间。

pkgName：安装包名称，安装包默认存放路径为 warden 容器里的 /root/helmchartpkg，以 emptydir 形式存在。

status：组件是否启用

env：环境变量配置

每一个要素都可以使用集群独特配置进行覆盖，未覆盖的要素则依然使用common里的配置。

## 管控集群配置说明

Pivot-cluster配置是管控集群配置，即KubeCube所在的集群。集群独特的配置会与common的配置结合，用于个性化配置集群组件，结合时遇到相同字段pivot-cluster优先。

```yaml
apiVersion: hotplug.kubecube.io/v1
kind: Hotplug
metadata:
  annotations:
    kubecube.io/sync: "true"			## 同步信号，kubecube会将这个配置同步到其他集群
  name: pivot-cluster					## 与集群名字一致，指明这是pivot-cluster这个集群的热插拔配置
spec:
  component:
  - name: logseer						## 日志管理组件
    status: enabled						## 结合common设置为disabled，这里设置为enabled，标识其余集群不启用，pivot-cluster集群启用
  - name: kubecube-monitoring
    env: |
      grafana:
        enabled: true
      prometheus:
        prometheusSpec:
          externalLabels:
            cluster: "{{.cluster}}"
          remoteWrite:
          - url: http://thanos-receive:19291/api/v1/receive
  - name: kubecube-thanos
    status: enabled
    env: |
      receive:
        replicaCount: 1
        replicationFactor: 1
status:
  message: '{"kubecube-monitoring":"release is running","kubecube-thanos":"release
    is running","logagent":"release is running","logseer":"release is running"}'
  phase: running

```

