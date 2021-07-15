---
title: "通知策略管理"
weight: 1
---

本文档介绍了如何在 KubeCube 中管理**项目级别**的告警联系人与通知策略。

## 准备工作

登录 KubeCube 平台并创建租户、项目、空间

## 联系人管理

> 推荐在一个告警通知策略内配置多个联系人，代表一个联系人组信息，而不是为每个联系人都创建一个通知策略。

1. 登录到 KubeCube 控制台，选择租户项目后，侧边栏展开【告警】菜单，选择【告警策略组】，并点击创建.

2. 弹出对话框后，填写告警联系人基本信息，目前页面支持配置Email、WeChat、Webhook三种联系方式，其他联系方式如Slack、OpsEngine等会在后续版本支持，如有需求可以通过列表页的【yaml配置】进行设置。

- 名称: 标识联系人组的名称，如"frontend"，在后续创建告警规则时作为关联。

#### Email配置

![create-amc-email](/imgs/user-guide/alerting/create-amc-email.png)

- 是否接收告警恢复通知：代表当告警规则不再触发后，是否发送通知。
- 收件人: 收件人的邮箱地址
- 更多配置：
注意：以下配置默认使用集群全局配置，可以询问集群管理员获知。
  - smarthost: 邮箱服务器域名和端口信息，e.g. imap.163.com:465
  - requireTLS: 是否需要启用TLS
  - from: 发件人邮箱
  - authUsername: 邮件服务器认证用户名
  - authPassword: 邮件服务器认证密码，需要提前创建一个Secret，再指定name和key
    - name: 已创建的Secret的名称
    - key: 已创建的Secret的key
更多配置请参考[EmailConfig](https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/api.md#emailconfig)

#### WeChat配置
![create-amc-wechat](/imgs/user-guide/alerting/create-amc-wechat.png)
- 是否接收告警恢复通知：代表当告警规则不再触发后，是否发送通知。
- toUser: 企业微信用户名
- toParty: 企业微信用户组
- toTag: 企业微信用户标签
- 更多配置：
注意：以下配置默认使用集群全局配置，可以询问集群管理员获知。
  - apiURL: 微信第三方通知的apiURL
  - corpID: 企业微信账号唯一 ID，可以在我的企业中查看。
  - agentID: 第三方企业应用的 ID，可以在已创建的第三方企业应用详情页面查看。
  - apiSecret: 需要根据第三方企业应用的密钥，提前创建一个Secret，再指定name和key
    - name: 已创建的Secret的名称
    - key: 已创建的Secret的key

更多配置请参考[企业微信文档](https://work.weixin.qq.com/api/doc/#10167/%E6%96%87%E6%9C%AC%E6%B6%88%E6%81%AF)以及[WeChatConfig](https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/api.md#wechatconfig)。


#### Webhook配置
![create-amc-webhook](/imgs/user-guide/alerting/create-amc-webhook.png)
- 是否接收告警恢复通知：代表当告警规则不再触发后，是否发送通知。
- url: Webhook的url，用来接受HTTP POST请求
- max_alerts: Alertmanager一次发往webhook通知中，包含告警的最大数量，当超过该值，告警会被截断，默认为全部发送。
更多配置参考[WebhookConfig](https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/api.md#webhookconfig)


## 高级功能

KubeCube 告警通知策略组支持高级策略配置，如
- 配置告警组内的发送一条告警通知的等待时间(group_wait)
- 配置告警组内发送两条告警通知的间隔时间(group_interval)
- 配置相同告警发送的间隔时间(repeat_interval)
- 配置嵌套的告警路由策略
- 配置告警抑制规则
- ...

如果需要实现更灵活的自定义通知策略，可以通过点击【列表】页面的【yaml设置】进行设置与修改，相关字段请参考[AlertmanagerConfig-CRD文档](https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/api.md#alertmanagerconfig), 具体字段含义请参考[Alertmanager配置](https://prometheus.io/docs/alerting/latest/configuration/)

