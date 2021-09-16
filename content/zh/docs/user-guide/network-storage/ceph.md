---
title: "Ceph集群"
weight: 2
---

本文档介绍了如何在 KubeCube 上接入 Ceph 集群。

## 示例环境说明

| 产品           | 版本    |
| :-------------| :----   |
| Kubernetes    | v1.20.9 |
| KubeCube      | v1.0.2  |
| Ceph          | 15.2.1  |

## 准备工作

**登录 Ceph master 节点**

1. 获取管理 key

   ```
   ceph auth get-key client.admin | base64
   ```

2. 在 Ceph 集群中创建一个 KubeCube 专用的 pool 和用户

   ```ssh
   ceph osd pool create kube 8 8
   ceph auth get-or-create client.kube mon 'allow r' osd 'allow class-read object_prefix rbd
   ```

3. 获取该用户 key

   ```
   ceph auth get-key client.kube｜base64
   ```

**登录 K8s 集群 worker 节点**

1. 安装 ceph-common

   `apt-get install ceph-common` 或 `yum install ceph-common`等。

2. 使用外部的 Provisioner 提供服务

   ```
   git clone https://github.com/kubernetes-incubator/external-storage.git    # v5.5.0
   cd external-storage/ceph/rbd/deploy 
   sed -r -i "s/namespace: [^ ]+/namespace: kube-system/g" ./rbac/clusterrolebinding.yaml ./rbac/rolebinding.yaml 
   kubectl -n kube-system apply -f ./rbac
   ```

## 创建 Secret

登录 KubeCube 所在节点，执行以下命令：

```ssh
# 替换为 Ceph 集群生成的 key
kubectl create secret generic ceph-secret --type="kubernetes.io/rbd" --from-literal=key='QVFEcGpqbGhqczJnQWhBQTByN3NNbHB4cTAwdGR1eWdqWk1LaUE9PQ==' --namespace=kube-system
kubectl create secret generic ceph-kube-secret --type="kubernetes.io/rbd" --from-literal=key='QVFDTHhUcGhMS0VMQWhBQWx3NU1MNzZneVRpR1dNYVdVckgxN0E9PQ==' --namespace=default
```

## 创建 StorageClass

方式一：命令行操作

`vi sc.yaml` 文件并 apply，以下为示例，需要根据 Ceph 集群信息修改参数。

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ceph-rbd
  annotations:
     storageclass.beta.kubernetes.io/is-default-class: "true"
provisioner: ceph.com/rbd
parameters:
  monitors: 10.173.32.173:6789  #修改成实际 Ceph Monitor IP
  adminId: admin
  adminSecretName: ceph-secret
  adminSecretNamespace: kube-system
  pool: kube
  userId: kube
  userSecretName: ceph-kube-secret
  userSecretNamespace: default
  fsType: ext4
  imageFormat: "2"
  imageFeatures: "layering"
```

关于上面的 adminId 等字段的具体含义请参考  [Ceph RBD](https://kubernetes.io/zh/docs/concepts/storage/storage-classes/#ceph-rbd)。

方式二：页面操作

1. 以平台管理员身份登录 KubeCube；
2. 展开左侧菜单栏里的【资源管理】，点击【集群管理】进入集群管理页面，点击需要创建  StorageClass 的集群名称，进入集群详情页面，点击【存储类别】进入存储类别管理页面，点击【创建存储类别】，将方式一中的文件内容写入，点击【确定】，即创建出该 StorageClass。

![ceph-1](/imgs/user-guide/network-storage/ceph/ceph-1.png)

## 创建 PVC

在控制台页面，选择租户和项目，选择集群和空间，点击左侧菜单栏【存储】进入存储管理页面。点击【创建存储声明】，存储类别选择 Ceph 对应的 StorageClass。

![nfs-2](/imgs/user-guide/network-storage/ceph/ceph-2.png)

创建后可以看到 PVC 状态为 Bound。具体配置说明见 [PVC管理](https://www.kubecube.io/docs/user-guide/ns-scoped-res/storage/pvc/)。

## 创建工作负载

在创建工作负载时，点击【展开更多配置】-【挂载数据卷】-【PVC】-【参数】，可以选择上述创建的 PVC。

![nfs-4](/imgs/user-guide/network-storage/nfs/nfs-4.png)

具体配置说明见 [工作负载管理](https://www.kubecube.io/docs/user-guide/ns-scoped-res/workload/)。

## 检查结果

检查相关 pod 是否正常运行，如果正常则配置成功。