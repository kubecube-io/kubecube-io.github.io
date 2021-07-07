---
title: "CronJob"
weight: 4
---

本文档介绍了如何在 KubeCube 上创建、使用和编辑 CronJob。

## 准备工作

创建一个租户，在租户下创建一个项目，在项目下一个创建一个命名空间，创建一个账号并赋予该命名空间操作权限。

## 创建 CronJob

1、选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击【CronJob】，进入 CronJob 管理页面。

![cronjob-apply](/imgs/user-guide/ns-scoped-res/workload/CronJob/cronjob-apply.png)

2、点击【部署】，编写 CronJob 的 yaml 文件。点击【确定】，即可部署该 CronJob。

CronJob 的 规范可参考：https://v1-20.docs.kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/。

## 管理 CronJob

选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击【CronJob】，进入  CronJob 管理页面。在管理页面，可以看到该命名空间下的所有 CronJob ，包括对应的名称、空间、状态、定时调度设置、正在运行的任务数以及创建时间。并可以在该界面对 CronJob 进行删除和修改操作。同时也可以根据名称对 CronJob 进行搜索。

![cronjob-manage](/imgs/user-guide/ns-scoped-res/workload/CronJob/cronjob-manage.png)

## 查看 CronJob 详情

在 CronJob 管理页面，点击任一 CronJob 名称，即可进入到该 CronJob 详情页。

![cronjob-detail](/imgs/user-guide/ns-scoped-res/workload/CronJob/cronjob-detail.png)

CronJob 详情页除了可以管理 CronJob，还可以查看 CronJob 的详细信息，根据状态过滤该 CronJob 关联的任务列表，以及查看该 CronJob 和该 CronJob 所关联的副本的事件。