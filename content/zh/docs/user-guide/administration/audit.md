---
title: "操作审计"
weight: 6
---

本文档介绍了如何在 KubeCube 上查询和导出操作审计日志。

## 准备工作

使用平台管理员账号登录 KubeCube。

## 开启操作审计

部署好 KubeCube 后，操作审计功能默认开启。如果需要关闭或开启操作审计功能：

1、使用平台管理员账号登录 KubeCube；

2、点击页面右上角【切换到控制台】，点击任意空间，进入到控制台页面；

3、在左侧菜单栏点击【自定义资源CRD】，进入到集群级别 CRD 列表，可以点击右上方输入 "hotplug" 进行搜索，找到 “hotplugs.hotplug.kubecube.io” CRD，点击【v1】版本进入 CRD 详情页；

4、选择 common 实例，点击【设置YAML】，找到 spec.component.name=audit，将 “status” 改成 “disabled”，即关闭审计功能；改为 “enabled”，为开启审计功能。详细配置说明见 [热插拔](https://www.kubecube.io/docs/installation-guide/enable-plugins/) 。

5、配置 ElasticSearch：

- 如果需要安装内置 ElasticSearch，修改上述 common 实例，找到 spec.component.name=elasticsearch，将 “status” 改成 “enabled”，在集群内安装 ElasticSearch。

- 如果需要连接外部 ElasticSearch，需要修改审计服务的deployment的环境变量：

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

- 如果同时配置了内部和外部 ElasticSearch，优先将审计日志发到外部 ElasticSearch。

## 查询审计日志

使用平台管理员账号登录 KubeCube 后，展开【管控运维】菜单，点击【操作审计】，进入操作审计页面。

![audit-manage](/imgs/user-guide/administration/audit/audit-manage.png)

如图所示，在该页面展示出了所有的审计日志，包括审计操作者的账号、操作的时间、IP地址、事件名称、资源、状态。同时平台管理员也可以根据账号、IP地址等进行过滤查询。

## 导出审计日志

在操作审计页面，对审计日志查询后，点击【导出】，即可对查询结果进行导出。导出文件格式为csv，导出的日志条数默认不超过10000条。