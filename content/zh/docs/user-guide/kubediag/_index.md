---
title: "排障系统"
weight: 8
---

本文档介绍了如何在 KubeCube 上集成 [KubeDiag](https://github.com/kubediag/kubediag) 排障系统。

## 准备工作

1. 由于 KubeDiag 使用 CertManager 进行证书管理，如果集群中已安装 CertManager，可跳过这一步骤， 否则可参考[官方文档](https://cert-manager.io/docs/installation/kubernetes/)进行安装，或运行以下命令进行快速安装。

```
kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.0.2/cert-manager.yaml
```

### 集成 KubeDiag

1、使用平台管理员账号登录 KubeCube；

2、点击页面右上角【切换到控制台】，点击任意空间，进入到控制台页面；

3. 在左侧菜单栏点击【自定义资源CRD】，进入到集群级别 CRD 列表，可以点击右上方输入 "hotplug" 进行搜索，找到 “hotplugs.hotplug.kubecube.io” CRD，点击【v1】版本进入 CRD 详情页；

4. 选择 common 实例，点击【设置YAML】，将 KubeDiag 的状态改为启用, 即可为所有集群开启kubediag系统，如下所示:
```yaml
    - name: kubediag
      namespace: kubediag
      pkgName: kubediag-helm-0.1.1.tgz
-     status: disabled
+     status: enabled
```

### 使用 KubeDiag

如果希望非集群管理员角色的平台用户也能使用 KubeDiag 的资源进行集群诊断操作，需要将 KubeDiag 提供的相关 CRD 操作权限接入 KubeCube 的内置角色中，具体操作如下(在执行以下操作前，需要获取 Kubernetes 集群`cluster-admin`角色的 KubeConfig )：

```sh
# 为平台所有角色赋予view权限
kubectl label clusterrole kubediag-view rbac.authorization.k8s.io/aggregate-to-reviewer=true
kubectl label clusterrole kubediag-view rbac.authorization.k8s.io/aggregate-to-project-admin=true
kubectl label clusterrole kubediag-view rbac.authorization.k8s.io/aggregate-to-tenant-admin=true
kubectl label clusterrole kubediag-view rbac.authorization.k8s.io/aggregate-to-platform-admin=true

# 为项目管理员以上级别的角色赋予edit权限
kubectl label clusterrole kubediag-edit rbac.authorization.k8s.io/aggregate-to-project-admin=true
kubectl label clusterrole kubediag-edit rbac.authorization.k8s.io/aggregate-to-tenant-admin=true
kubectl label clusterrole kubediag-edit rbac.authorization.k8s.io/aggregate-to-platform-admin=true
```

完成以上操作后，即可使用平台用户登录 KubeCube 平台，在【自定义资源CRD】页面内，完成对 KubeDiag 相关 CRD 的管理，具体配置方式参考 KubeDiag [API文档](https://github.com/kubediag/kubediag/tree/master/docs/api)

![create-diag](/imgs/user-guide/kubediag/create-diagnosis.png)


