---
title: "Job"
weight: 3
---

本文档介绍了如何在 KubeCube 上创建、使用和编辑 Job。

## 准备工作

创建一个租户，在租户下创建一个项目，在项目下一个创建一个命名空间，创建一个账号并赋予该命名空间操作权限。

## 创建 Job

1、选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击【Job】，进入 Job 管理页面。

![job-apply](/imgs/user-guide/ns-scoped-res/workload/Job/job-apply.png)

2、点击【部署】，编写 job 的 yaml 文件。点击【确定】，即可部署该 job。

job 的 规范可参考：https://v1-20.docs.kubernetes.io/docs/concepts/workloads/controllers/job/。

## 管理 Job

选择租户和项目，选择集群和空间，展开【工作负载】菜单，点击【Job】，进入  Job 管理页面。在管理页面，可以看到该命名空间下的所有 job 名称以及对应的状态、执行情况、运行时长，并可以在该界面对 job 进行删除操作。同时也可以根据名称对 job 进行搜索。

![job-manage](/imgs/user-guide/ns-scoped-res/workload/Job/job-manage.png)

## 查看 Job 详情

在 Job 管理页面，点击任一 job 名称，即可进入到该 job 详情页。

![job-detail](/imgs/user-guide/ns-scoped-res/workload/Job/job-detail.png)

Job 详情页除了可以管理 Job，还可以查看 Job 的详细信息，关联的副本信息和副本的监控数据，以及  Job 和关联副本的事件和 condition 信息。