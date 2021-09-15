---
title: "部署环境要求"
weight: 1
---
在进行 All In One 或者多节点部署之前，请按照以下内容确认环境要求

# 系统版本及硬件要求

| 操作系统                     | 最低要求                             |
| :--------------------------- | :----------------------------------- |
| **Ubuntu** *16.04, 18.04*    | CPU：4 核，内存：8 G，磁盘空间：20 G |
| **Debian** *Buster, Stretch* | CPU：4 核，内存：8 G，磁盘空间：20 G |
| **CentOS** *7*.x             | CPU：4 核，内存：8 G，磁盘空间：20 G |
| **Kylin**  *v10*             | CPU：4 核，内存：8 G，磁盘空间：20 G |

| OS ARCH                      | 硬件要求                             |
| :--------------------------- | :----------------------------------- |
| **AMD 64**                   | Intel 系列，AMD 系列     |
| **ARM 64**                   | Phytium 2000（国产化arm64芯片理论上都支持，但未经过充分测试）           |

> 以上系统配置要求适用于 KubeCube 默认最小化 All In One 模式安装，如需启动更多可插拔组件和拓展功能，建议机器配置为 8 核 CPU 和 16 G 内存

# 容器运行时

| 支持的容器运行时 | 版本     |
| :--------------- | :------- |
| Docker           | 19.3.12+ |

> 节点上若无容器运行时，部署脚本将自动安装 docker 19.03.12 作为容器运行时

# Kubernetes 版本
KubeCube 支持的 k8s 版本为 v1.18 ~ v1.21
KubeCube 部署脚本支持的 k8s 版本为 v1.18.20、v1.19.13、v1.20.9、v1.21.2

# 前置准备

在使用部署脚本开始 KubeCube 的安装时，脚本会检测环境，并提示需要安装缺失的依赖

![env-check](/imgs/installation-guide/requirement/env-check.png)

# 监控组件说明

KubeCube 会默认安装 Prometheus 等监控组件，如果选择在已有k8s集群中部署 KubeCube，并且集群中已安装 Mertics Server，安装 KubeCube 后 Prometheus 会和 Mertics Server 产生冲突，导致监控功能不可用。需要在安装 KubeCube 后执行以下步骤：

1. 点击页面右上角【切换到控制台】，点击任意空间，进入到控制台页面；

2. 在左侧菜单栏点击【自定义资源CRD】，进入到集群级别 CRD 列表，可以点击右上方输入 “hotplug” 进行搜索，找到 “hotplugs.hotplug.kubecube.io” CRD，点击【v1】版本进入 CRD 详情页；

3. 选择 common 实例，点击【设置YAML】，找到 spec.component. name=kubecube-monitoring，添加环境变量 prometheusAdapter.enabled=false，如：

```yaml
  - env: |
      grafana:
        enabled: false
      prometheus:
        prometheusSpec:
          externalLabels:
            cluster: "{{.cluster}}"
          remoteWrite:
          - url: http://10.173.32.129:31291/api/v1/receive
      prometheusAdapter:
  			enabled: false
    name: kubecube-monitoring
    namespace: kubecube-monitoring
    pkgName: kubecube-monitoring-15.4.10.tgz
    status: enabled
```