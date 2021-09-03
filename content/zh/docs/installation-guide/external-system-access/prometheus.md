---
title: "Prometheus"
weight: 1
---

KubeCube 在部署时会自动安装 Prometheus 等监控组件，以实现监控功能。本文档介绍了如何在 KubeCube 中接入用户已有的 Prometheus。

## 准备工作

1. 在集群部署好 Prometheus，Prometheus 可以正常监控到集群资源的数据；

2. 在集群中部署好 KubeCube；

   说明：部署好 KubeCube 后，由于集群内已有 Prometheus operator，多个 operator 会导致集群内 Prometheus 相关功能不可用，需要卸载 KubeCube 监控组件或删除本地 operator。

3. 以平台管理员角色登录 KubeCube 管控集群。

## 步骤

### 1、添加 Label

由于 KubeCube 需要实现多集群监控，因此在 KubeCube 查询监控数据时，都会在 query 表达式中添加 `cluster={clusterName}` 来进行集群过滤。用户需要在 Prometheus 的 exporter 中添加这一 label，前端查询监控数据时才能查询到结果。

### 2、卸载 KubeCube 监控组件

方式一 页面操作：

1. 点击页面右上角【切换到控制台】，点击任意空间，进入到控制台页面；

2. 在左侧菜单栏点击【自定义资源CRD】，进入到集群级别 CRD 列表，可以点击右上方输入 “hotplug” 进行搜索，找到 “hotplugs.hotplug.kubecube.io” CRD，点击【v1】版本进入 CRD 详情页；

3. 选择 common 实例，点击【设置YAML】，找到 spec.component. name=kubecube-monitoring，将 “status” 改成 “disabled”，即卸载 KubeCube 自带监控组件。

方式二 命令行操作：

1. `kubectl edit hotplug common`
2. 找到 spec.component. name=kubecube-monitoring，将 “status” 改成 “disabled”。

详细配置说明见 [热插拔](https://www.kubecube.io/docs/installation-guide/enable-plugins/) 。

### 3、部署 ServiceMonitor

#### 查看集群资源

查看控制台内监控数据，需要部署两个 ServiceMonitor：kubelet 和 kube-state-metrics 的 ServiceMonitor，样例如下：

- 部署 kubecube-monitoring-kubelet

  ```yaml
  apiVersion: monitoring.coreos.com/v1
  kind: ServiceMonitor
  metadata:
    name: kubecube-monitoring-kubelet
    namespace: kubecube-monitoring
  spec:
    endpoints:
    - bearerTokenFile: /var/run/secrets/kubernetes.io/serviceaccount/token
      honorLabels: true
      port: https-metrics
      relabelings:
      - sourceLabels:
        - __metrics_path__
        targetLabel: metrics_path
      scheme: https
      tlsConfig:
        caFile: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        insecureSkipVerify: true
    - bearerTokenFile: /var/run/secrets/kubernetes.io/serviceaccount/token
      honorLabels: true
      path: /metrics/cadvisor
      port: https-metrics
      relabelings:
      - sourceLabels:
        - __metrics_path__
        targetLabel: metrics_path
      scheme: https
      tlsConfig:
        caFile: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        insecureSkipVerify: true
    - bearerTokenFile: /var/run/secrets/kubernetes.io/serviceaccount/token
      honorLabels: true
      path: /metrics/probes
      port: https-metrics
      relabelings:
      - sourceLabels:
        - __metrics_path__
        targetLabel: metrics_path
      scheme: https
      tlsConfig:
        caFile: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        insecureSkipVerify: true
    jobLabel: k8s-app
    namespaceSelector:
      matchNames:
      - kube-system
    selector:
      matchLabels:
        k8s-app: kubelet
  ```

- 部署 kubecube-monitoring-kube-state-metrics

  ```yaml
  apiVersion: monitoring.coreos.com/v1
  kind: ServiceMonitor
  metadata:
    name: kubecube-monitoring-kube-state-metrics
    namespace: kubecube-monitoring
  spec:
    endpoints:
    - honorLabels: true
      port: http
    selector:
      matchLabels:
        app.kubernetes.io/instance: kubecube-monitoring
        app.kubernetes.io/name: kube-state-metrics
  ```

#### 查看组件监控

当前 KubeCube 平台支持对组件的监控视图可视化查询，详细说明见 [平台组件监控](https://www.kubecube.io/docs/user-guide/monitoring/component-monitoring/) 。接入外部监控后，用户可按需在集群内部署对应组件的 ServiceMonitor。

各个 ServiceMonitor 的 yaml 可参考 https://github.com/kubecube-io/charts/tree/main/kubecube-monitoring/templates/exporters。

### 4、部署 Dashboard

查看集群资源（控制台内监控数据），需要部署：

- [cube-resource-cluster.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/cube-resource-cluster.yaml)
- [cube-resource-namespace.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/cube-resource-namespace.yaml)
- [cube-resource-node.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/cube-resource-node.yaml)
- [cube-resource-persistentvolume.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/cube-resource-persistentvolume.yaml)
- [cube-resource-pod.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/cube-resource-pod.yaml)
- [cube-resource-workload.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/cube-resource-workload.yaml)
- [default-rolebinding.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/default-rolebinding.yaml)

查看组件监控，可以部署对应的 Dashboard：

- [component-control-plane-pods.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/component-control-plane-pods.yaml)
- [component-coredns.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/component-coredns.yaml)
- [component-etcd.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/component-etcd.yaml)
- [component-kube-apiserver.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/component-kube-apiserver.yaml)
- [component-kube-controller-manager.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/component-kube-controller-manager.yaml)
- [component-kube-proxy.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/component-kube-proxy.yaml)
- [component-kube-scheduler.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/component-kube-scheduler.yaml)
- [component-kubelet.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/component-kubelet.yaml)
- [component-prometheus.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/component-prometheus.yaml)
- [component-thanos.yaml](https://github.com/kubecube-io/charts/blob/main/kubecube-monitoring/templates/kubecube-addon/dashboards/component-thanos.yaml)

### 5、修改 Nginx 配置

1. 使用命令行：`kubectl edit configmap nginx-config -n kubecube-system`

2. 找到原有的地址配置，修改为自有 Prometheus 地址

    ```yaml
    upstream monitoring {
        server kubecube-thanos-query.kubecube-monitoring:9090;
    }
    ```
    
    即 将`kubecube-thanos-query.kubecube-monitoring:9090` 替换为外部地址。

3. 重启 pod：`kubectl delete pod frontend-xxxxxx-xxxxx -n kubecube-system`

### 