---
title: "告警规则管理"
weight: 4
---

本文档介绍了如何在 KubeCube 中管理**项目级**告警规则。


## 准备工作

1. 登录 KubeCube 平台并创建租户、项目、空间
2. 创建告警通知策略

## 告警规则管理
#### 创建告警规则

1. 登录到 KubeCube 控制台，选择租户项目后，侧边栏展开【告警】菜单，选择【告警规则】，并点击创建.
2. 填写告警规则基本信息：

![create-alertrule](/imgs/user-guide/alerting/create-alertrule.png)

- 告警名称: 告警规则名称
- 表达式：promql表达式，具体配置方式请参考[prometheus文档](https://prometheus.io/docs/prometheus/latest/querying/basics/)
- 持续时间：代表告警规则表达式持续被触发的时长，如果达到该期望时间，就触发一条告警。
- 告警程度：告警程度信息
- 通知策略组：选择告警触发后需要通知的联系人与相应的通知策略。
- 告警描述信息: 告警触发后发送的告警描述信息，具体配置方式请参考[Prometheus告警规则模版配置](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/#templating)

#### 告警状态查询
![list-alertrule](/imgs/user-guide/alerting/list-alertrule.png)

在告警规则列表页面，可以查看项目下所有告警规则的状态，状态包含以下三种：
- normal: 代表该告警规则未触发告警
- pending: 代表告警规则被触发，但未达到期望持续时间。
- firing：代表告警规则被触发，并达到期望持续时间。

点击【告警名称】的状态，可以查看触发该告警规则的具体对象信息，如空间信息，Pod信息等，静默功能将在后续版本中支持。