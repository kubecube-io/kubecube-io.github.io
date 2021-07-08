---
title: "产品架构"
weight: 2
---

KubeCube 产品由 KubeCube Service、Warden、CloudShell和 AuditLog Server 等组件组成，除了 Warden 部署在各个 Kubernetes 集群充当认证代理，其余组件均部署在管理集群。下图描述的 KubeCube 整体产品架构，包括与用户的交互，与 Kubernetes API Server 交互，Prometheus 监控和网易轻舟自研日志采集组件。

![architecture](/imgs/overview/architecture.png)

- 用户可以通过 KubeCube UI、CLI 指令、Open API 访问 Kubecube 服务和 Kubernetes 资源，其中 CLI 功能主要由 CloudShell 组件提供。
- KubeCube Service 实现统一认证服务，透传代理 Kubernetes 资源请求，和提供更丰富的资源请求扩展接口。KubeCube Servie 包含四个组件：Restful API Server 提供API支持，AuditLog 负责审计日志收集和发送审计日志到处理组件 KubeCube AuditLog Server，Controller Manager 实现资源的 Reconcile 和 Validate Webhook ，Scout 实现各个集群之间资源的同步。
- K8s APIServer 使用 Admission Webhook 的形式向 Warden（哨兵守卫）请求身份认证，并将操作审计日志上传给 KubeCube Service。
- KubeCube Warden（哨兵守卫）负责身份认证和集群健康上报，部署在每一个业务集群，即使业务集群与管理集群脱离，依然可以实现认证和集群自治。
- 集成 Prometheus + AlertManager + Thanos 监控告警解决方案和网易轻舟自研的容器日志采集解决方案 Logseer + Logagent。
- 考虑到性能表现和可维护性因素，我们建议使用管理 Kubernetes 集群和业务 Kubernetes 集群，分开部署 Kubecube 服务和 Kubernetes 工作负载，支持对接多个业务 Kubernetes 集群。





