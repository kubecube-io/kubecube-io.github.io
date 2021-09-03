---
title: "ElasticSearch"
weight: 2
---

KubeCube 提供了日志服务和操作审计服务，默认关闭。用户在开启后，日志服务和操作审计服务均会将日志发送到 ElasticSearch 进行存储，由 ElasticSearch 对日志进行管理。用户可以在 [热插拔](https://www.kubecube.io/docs/installation-guide/enable-plugins/) 中修改配置，安装内部 ElasticSearch，也可以配置外部的 ElasticSearch 地址，对接已有的 ElasticSearch。下面分别介绍如何在这两个功能中接入外部 ElasticSearch。

## 日志

方式一 页面操作：

1. 点击页面右上角【切换到控制台】，点击任意空间，进入到控制台页面；

2. 在左侧菜单栏点击【自定义资源CRD】，进入到集群级别 CRD 列表，可以点击右上方输入 “hotplug” 进行搜索，找到 “hotplugs.hotplug.kubecube.io” CRD，点击【v1】版本进入 CRD 详情页；

3. 选择 common 实例，点击【设置YAML】，找到 spec.component. name=logseer，添加环境变量，如：

```yaml
- name: logseer
  namespace: logseer
  pkgName: logseer-v1.0.0.tgz
  status: disabled
  env: |
  address: elasticsearch-master.elasticsearch.svc
```

方式二 命令行操作：

1. `kubectl edit hotplug pivot-cluster`
2. 找到 spec.component. name=logseer，添加环境变量，同上。

详细配置说明见 [热插拔](https://www.kubecube.io/docs/installation-guide/enable-plugins/) 。


## 操作审计

1. `kubectl edit deploy audit -n kubecube-system`

2. 添加环境变量：AUDIT_WEBHOOK_HOST、AUDIT_WEBHOOK_INDEX、AUDIT_WEBHOOK_TYPE，如

   ```yaml
   env:
   - name: AUDIT_WEBHOOK_HOST
     value: http://elasticsearch-master.elasticsearch:9200
   - name: AUDIT_WEBHOOK_INDEX
     value: audit
   - name: AUDIT_WEBHOOK_TYPE
     value: logs
   ```

注：如果同时配置了内部和外部 ElasticSearch，审计日志将优先发到外部 ElasticSearch。

其他详细说明见：[操作审计](https://www.kubecube.io/docs/user-guide/administration/audit/) 。


