---
title: "平台组件告警"
weight: 1
---

本文档介绍了如何在 KubeCube 中配置**平台级**告警，包括配置集群内的 Alertmanager 、告警通知联系人、通知路由规则、告警规则。

## 简介
默认情况下，KubeCube 会在平台部署`kubecube-monitoring` Chart，该 Chart 包含 `Alertmanager` 组件和默认的 `Alertmanager Config Secret`配置以及平台基础组件的告警规则。默认情况下，KubeCube 创建的 `Alertmanager Config Secret` 不会因为 Chart 的升级或删除操作而被修改，以防用户的配置丢失。

## 准备工作
以平台管理员角色登录 KubeCube 平台。

## 配置 AlertManager

登录到 KubeCube 平台，点击【运维管理】，侧边栏选择【告警--全局告警配置】，列表页可以看到各个集群的`AlertManager` 的配置信息，点击【设置】按钮进行配置，

### 全局配置

![am-global](/imgs/user-guide/alerting/am-global.png)

若使用企业邮箱作为告警通知方式，需要在全局配置中配置以下字段：
- `smtp_smarthost` : 邮箱服务器域名和端口信息，e.g. imap.163.com:465
- `smtp_from` : 发件人邮箱
- `smtp_auth_username` : 邮件服务器认证用户名
- `smtp_auth_password` : 邮件服务器认证密码或授权码

若使用企业微信作为告警通知方式，需要在全局配置中配置以下字段：
- `wechat_api_url` : 默认使用`https://qyapi.weixin.qq.com/cgi-bin/`
- `wechat_api_secret` : 第三方企业应用的密钥
- `wechat_api_corp_id` : 企业微信账号唯一 ID

### 通知方式

目前页面支持配置Email、WeChat、Webhook三种联系方式，其他联系方式如Slack、OpsEngine等会在后续版本支持
![am-receivers](/imgs/user-guide/alerting/am-receivers.png)

更多字段含义请参考[Alertmanager官方文档中关于receivers的定义](https://www.prometheus.io/docs/alerting/latest/configuration/#receiver)

### 通知路由规则

相关配置如下：
- `receiver` : 选择上一步骤中定义的联系人
- `group_by` : 当前 route 节点的分组规则
- `matchers` : 当前 route 节点的匹配规则
- `group_wait` : 告警组内的发送一条告警通知的等待时间
- `group_interval` : 告警组内发送两条告警通知的间隔时间
- `repeat_interval` : 相同告警发送的间隔时间


![am-route](/imgs/user-guide/alerting/am-route.png)

更多字段含义请参考[Alertmanager官方文档中关于route的定义](https://www.prometheus.io/docs/alerting/latest/configuration/#route)，当前页面暂不支持子路由的配置，会在后续版本提供支持。

## 管理告警规则

### 查看告警规则组
登录到 KubeCube 平台，点击【运维管理】，侧边栏选择【告警--告警规则】，列表页可以看到各个集群的`PrometheusRule` 的配置信息，默认情况下， KubeCube 为每个集群内置了基础资源以及平台组件的 `PrometheusRule`,

![component-alerting](/imgs/user-guide/alerting/component-alerting.png)

### 配置告警规则内容
可以点击【设置】按钮查看并配置每条告警规则的具体内容，包括
- 表达式 : `Promql`表达式
- `for` : 告警持续时长
- 告警程度 : 可以在上述 [通知方式](#通知方式)中配置不同告警程度对应的 Receiver 来接收告警通知
- Annotations
  - 摘要: 接收告警通知的摘要信息
  - 描述信息: 接收告警通知的具体描述信息，如发生故障的 Pod 所在的集群，空间等
  - `Runbook Url`: 针对该告警规则的运维排障文档，应作为最佳实践在企业内部进行维护
  - 也可以【展开更多配置】，添加更多自定义的Annotations(键-值对)
- Labels: 为告警规则附带的标签信息(键-值对)，可以配合[通知路由规则](#通知路由规则) 实现告警通知的高级配置

![component-PrometheusRule](/imgs/user-guide/alerting/component-prometheusRule.png)

更多字段含义请参考[Prometheus-Operator的API文档](https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/api.md#rule)